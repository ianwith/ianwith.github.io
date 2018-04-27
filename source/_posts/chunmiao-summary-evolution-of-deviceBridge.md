title: 春苗项目小结之deviceBridge的进化之路
date: 2015-03-04 14:03:46
tags:
  - work
  - front end
  - summary
  - js
  - native
  - chunmiao
categories: work
---

deviceBridge是我们连接WebView页面与Native App的桥梁，它实现了JS和NativeCode之间的相互通信。春苗移动端App是一个HybridApp，内嵌了Wap版的帖子详情页面，其中包含的许多功能逻辑需要和NA协作共同完成，这就需要一套完备的交互接口。为了说明deviceBridge的工作原理，我们挑选一个最复杂的需求“回复”来理一理整个流程。所谓一通百通，跑通了一个功能，其他的也就迎刃而解了。

<img src="/img/chunmiao-summary-evolution-of-deviceBridge/reply_flow.png" alt="回复流程" style="width:882px">

这里主要来看前端JS的接口部分，Native端的处理代码就暂时忽略了。下面我就说说在编写这个JS脚本时候我的学习和优化过程。
<!-- more -->

##第一版
我们做了个简单的demo以确定NA与JS双方的交互方式。虽然了解到Android中addJavascriptInterface可以将接口注入到JS中，但是这种方式并不安全。所以我们采用伪协议的方式来实现相互间的通信。最开始的时候是这么做的：
```js
window.deviceBridge = {};

deviceBridge = {
    /**
     * 发起请求
     */
    _doRequest: function (action, args) {
        var reqData = {
            action: action,
            args: args
        };
        window.location = "chunmiao://#/" + JSON.stringify(reqData);
    },
    /**
     * 发起回复，NA调起回复框和键盘
     */
    causeReply: function (params) {
        var action = 'REPLY';
        var args = JSON.stringify(params);
        this._doRequest(action, args);
    },
    /**
     * 向服务器发送回复
     */
    sendComment: function (res) {
        var args = JSON.parse(res);
        try {
            // request server api
            window['send_reply'](args);
        } catch(e) {}
    },
    /**
     * 回复结果状态通知NA
     */
    replyStatus: function (res) {
        var action = 'REPLY_RESULT';
        var args = JSON.stringify(res.status);
        this._doRequest(action, args);
    }
}
```
这个版本只是实现了基本的接收发送消息的功能，它留下了很多问题：各个方法独立调用，流程没有有效衔接起来。当有多次请求时，无法辨别对应的哪个响应。

##第二版
加入回调逻辑，并为每个回调生成唯一标识的ID，防止流程混乱。iOS和Android采用不同的通信方式：Android利用prompt方式传参，消息可以被Java端WebChromeClient.onJsPrompt拦截到；iOS由于无法拦截prompt，继续采用伪协议通信。
```js
window.deviceBridge = {};

deviceBridge = {
    /**
     * 生成唯一的编号
     */
    getReqID: function () {
        return '_bd_' + new Date().getTime();
    },
    /**
     * 初始化回调函数
     */
    initCallback: function(callback, nReqID) {
        callbackMethodName = 'callbackjs' + nReqID;
        window[callbackMethodName] = function(res){
            window[callbackMethodName] = null;
            callback(res);
        }
    },
    /**
     * 开始请求，分别采用不同的通信方式
     */
    _sendRequest: function (action, args, callbackMethodName) {
        var isAndroid = /Android/.test(navigator.userAgent);
        var reqData = {
            action: action,
            args: args
        };
        if (isAndroid) {
            if (callbackMethodName != null) {
                prompt(JSON.stringify(reqData), "javascript:" + callbackMethodName + "('%1$s')");
            }
            else {
                prompt(JSON.stringify(reqData));
            }
        }
        else {
            if (callbackMethodName != null) {
                reqData.callback = callbackMethodName;
            }
            window.location = "chunmiao://#/" + JSON.stringify(reqData);
        }
    },
    /**
     * 发送请求入口
     */
    _doRequest: function (action, args, callback) {
        var nReqID = this.getReqID();
        if (callback) {
            this.initCallback(callback, nReqID);
            this._sendRequest(action, args, callbackMethodName);
        }
        else {
            this._sendRequest(action, args);
        }
    },
    /**
     * 发起回复
     */
    causeReply: function (params) {
        var action = 'REPLY';
        var args = JSON.stringify(params);
        this._doRequest(action, args, this.sendComment);
    },
    /**
     * 向服务器发送回复
     */
    sendComment: function (res) {
        var args = JSON.parse(res);
        try {
            // request server api
            window['send_reply'](args);
        } catch(e) {}
    },
    /**
     * 回复结果状态
     */
    replyStatus: function (res) {
        var action = 'REPLY_RESULT';
        var args = JSON.stringify(res.status);
        this._doRequest(action, args);
    }
}
```
所以我们可以这么用：
```js
/**
 * 页面事件
 */
new TapEvents({
    container: $('#pages-container'),
    preventDefault: false,
    bind: {
        'reply': function (target) {
            if (isNa) {
                deviceBridge.causeReply({
                    'parent_id': target.attr('data-id'), 
                    'parent_user': target.attr('data-parent-name')
                });
            } else {
                return;
            }
        }
    }
});
/**
 * 发起后端数据API请求方法
 */
var send_reply = function (options) {
    $.ajax({
        url: '/api/reply',
        data: data,
        type: 'post'
    }).done(function(data){
        deviceBridge.replyStatus(data);
    });
};
```
整个流程的时序图大致是这样的：

<img src="/img/chunmiao-summary-evolution-of-deviceBridge/method_sequence.png" alt="时序图" style="width:882px">

在换用prompt的时候，我们踩到一个坑，Android只能收到一次消息，后面的消息再也无法接收。这里要提醒的是，Android端别忘了confirm，不然就会阻塞了。

##第三版
添加全流程标识ID；加入命名空间，统一构建所有API方法；支持新需求：在NA端直接触发回复，回复框输入，之后将回复内容塞给JS。
```js
(function(){
    var isAndroid = /Android/.test(navigator.userAgent);
    var callbackMethodName = null;
    /**
     * 与NA约定的Action名称
     */
    var cmdMap = {
        causeReply: "REPLY",
        replyStatus: "REPLY_RESULT",
        sendComment: "SEND"
    };
    /**
     * 接口池
     */
    var cmdPool = {
        "deviceBridge.device.causeReply": function (action, params) {
            doRequest(action, params, deviceBridge.call.sendComment);
        },
        "deviceBridge.call.sendComment": function (action, params) {
            var args = JSON.parse(params);
            if (!args.actionID) {
                // 如果是NA直接触发回复
                args.actionID = getReqID();
            }
            try {
                window['send_reply'](args); // request server api
            } catch(e) {}
        },
        "deviceBridge.result.replyStatus": function (action, params) {
            doRequest(action, params);
        }
    };
    function getReqID () {
        return '_bd_' + new Date().getTime();
    }
    function initCallback (callback, nReqID) {
        callbackMethodName = 'callbackjs' + nReqID;
        window[callbackMethodName] = function(res){
            window[callbackMethodName] = null;
            callback(res);
        }
    }
    function sendRequest (action, nReqID, args, callbackMethodName) {
        var reqData = {
            action: action,
            actionID: nReqID,
            args: args
        };
        if (isAndroid) {
            if (callbackMethodName != null) {
                prompt(JSON.stringify(reqData), "javascript:" + callbackMethodName + "('%1$s')");
            }
            else {
                prompt(JSON.stringify(reqData));
            }
        }
        else {
            if (callbackMethodName != null) {
                reqData.callback = callbackMethodName;
            }
            window.location = "chunmiao://#/" + JSON.stringify(reqData);
        }
    }
    function doRequest (action, args, callback) {
        var nReqID = getReqID();
        if (callback) {
            initCallback(callback, nReqID);
            sendRequest(action, nReqID, args, callbackMethodName);
        }
        else {
            sendRequest(action, nReqID, args);
        }
    }
    /**
     * 注册命名空间
     */
    function ns (namespace, owner) {
        var names = namespace.split(".");
        owner = owner || window;
        for (var i = 0; i < names.length; i++) {
            var packageName = names[i];
            owner[packageName] = owner[packageName] || {};
            owner = owner[packageName];
        }
        return owner;
    }
    /**
     * 固化参数Action名
     */
    function currying (fn, cmd) {
        return function () {
            var args = [cmd];
            args.push.apply(args, arguments);
            return fn.apply(this, args);
        }
    }
    /**
     * 生成方法
     */
    function bindFn (cmdStr, fn) {
        var parts = cmdStr.split(".");
        var method = parts.pop();
        var prefix = parts.join(".");
        var namespace = ns(prefix);
        namespace[method] = currying(fn, cmdMap[method]);
    }
    /**
     * 构建接口
     */
    function buildInterface () {
        for (var key in cmdPool) {
            bindFn(key, cmdPool[key]);
        }
    }

    buildInterface();
})();
```
我们在向服务器发送数据请求的时候，也需要记住当前流程的ID
```js
var send_reply = function (params) {
    var actionID = params.actionID;
    var data = params.data;
    $.ajax({
        url: '/api/reply',
        data: data,
        type: 'post'
    }).done(function(data){
        var res = JSON.stringify({
            actionID: actionID,
            status: data.status
        });
        deviceBridge.result.replyStatus(res);
    });
};
```

##后续优化
1. 优化回调函数，采用dispatch方法统一分发响应。
2. 添加错误处理机制。
3. 增加调试模式，方便前端在浏览器调试代码。
...