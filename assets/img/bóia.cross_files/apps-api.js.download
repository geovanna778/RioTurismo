var AppsApi = new (function() {

    var _wz = this;

    // utils functions
    var Utils = {
        extend: function () {
            var obj = arguments[0] || {};
            for (var i = 1; i < arguments.length; i++) {
                var source = arguments[i];
                if (source) {
                    for (var prop in source) {
                        obj[prop] = source[prop];
                    }
                }
            }
            return obj;
        },
        addEvent: function(elem, type, eventHandle) {
            if (elem == null || typeof(elem) == 'undefined') return;
            if ( elem.addEventListener ) {
                elem.addEventListener( type, eventHandle, false );
            } else if ( elem.attachEvent ) {
                elem.attachEvent( "on" + type, eventHandle );
            } else {
                elem["on"+type]=eventHandle;
            }
        },
        parseQueryParams: function() {
            var query = window.location.search.substring(1) || '';
            var queries = query.split('&');
            var params = {};
            for (var i = 0; i < queries.length; i++) {
                var p=queries[i].split('=');
                if (p.length != 2) continue;
                params[decodeURIComponent(p[0])] = decodeURIComponent(p[1].replace(/\+/g, " "));
            }return params;
        },
        isArray: function(array) {
            return Object.prototype.toString.call( array ) === '[object Array]';
        },
        Base64: {
            // private property
            _keyStr : "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=",

            // public method for encoding
            encode : function (input) {
                var output = "";
                var chr1, chr2, chr3, enc1, enc2, enc3, enc4;
                var i = 0;

                input = Utils.Base64._utf8_encode(input);

                while (i < input.length) {

                    chr1 = input.charCodeAt(i++);
                    chr2 = input.charCodeAt(i++);
                    chr3 = input.charCodeAt(i++);

                    enc1 = chr1 >> 2;
                    enc2 = ((chr1 & 3) << 4) | (chr2 >> 4);
                    enc3 = ((chr2 & 15) << 2) | (chr3 >> 6);
                    enc4 = chr3 & 63;

                    if (isNaN(chr2)) {
                        enc3 = enc4 = 64;
                    } else if (isNaN(chr3)) {
                        enc4 = 64;
                    }

                    output = output +
                        this._keyStr.charAt(enc1) + this._keyStr.charAt(enc2) +
                        this._keyStr.charAt(enc3) + this._keyStr.charAt(enc4);

                }

                return output;
            },

            // public method for decoding
            decode : function (input) {
                var output = "";
                var chr1, chr2, chr3;
                var enc1, enc2, enc3, enc4;
                var i = 0;

                input = input.replace(/[^A-Za-z0-9\+\/\=]/g, "");

                while (i < input.length) {

                    enc1 = this._keyStr.indexOf(input.charAt(i++));
                    enc2 = this._keyStr.indexOf(input.charAt(i++));
                    enc3 = this._keyStr.indexOf(input.charAt(i++));
                    enc4 = this._keyStr.indexOf(input.charAt(i++));

                    chr1 = (enc1 << 2) | (enc2 >> 4);
                    chr2 = ((enc2 & 15) << 4) | (enc3 >> 2);
                    chr3 = ((enc3 & 3) << 6) | enc4;

                    output = output + String.fromCharCode(chr1);

                    if (enc3 != 64) {
                        output = output + String.fromCharCode(chr2);
                    }
                    if (enc4 != 64) {
                        output = output + String.fromCharCode(chr3);
                    }

                }

                output = Utils.Base64._utf8_decode(output);

                return output;

            },

            // private method for UTF-8 encoding
            _utf8_encode : function (string) {
                string = string.replace(/\r\n/g,"\n");
                var utftext = "";

                for (var n = 0; n < string.length; n++) {

                    var c = string.charCodeAt(n);

                    if (c < 128) {
                        utftext += String.fromCharCode(c);
                    }
                    else if((c > 127) && (c < 2048)) {
                        utftext += String.fromCharCode((c >> 6) | 192);
                        utftext += String.fromCharCode((c & 63) | 128);
                    }
                    else {
                        utftext += String.fromCharCode((c >> 12) | 224);
                        utftext += String.fromCharCode(((c >> 6) & 63) | 128);
                        utftext += String.fromCharCode((c & 63) | 128);
                    }

                }

                return utftext;
            },

            // private method for UTF-8 decoding
            _utf8_decode : function (utftext) {
                var string = "";
                var i = 0;
                var c = c1 = c2 = 0;

                while ( i < utftext.length ) {

                    c = utftext.charCodeAt(i);

                    if (c < 128) {
                        string += String.fromCharCode(c);
                        i++;
                    }
                    else if((c > 191) && (c < 224)) {
                        c2 = utftext.charCodeAt(i+1);
                        string += String.fromCharCode(((c & 31) << 6) | (c2 & 63));
                        i += 2;
                    }
                    else {
                        c2 = utftext.charCodeAt(i+1);
                        c3 = utftext.charCodeAt(i+2);
                        string += String.fromCharCode(((c & 15) << 12) | ((c2 & 63) << 6) | (c3 & 63));
                        i += 3;
                    }

                }

                return string;
            }
        }
    };

    var Events = this.Events = {
        PROPERTY_CHANGE: 'PROPERTY_CHANGE',
        SETTING_CHANGE: 'SETTING_CHANGE',
        STYLE_CHANGE: 'STYLE_CHANGE',
        SIZE_CHANGE: 'SIZE_CHANGE',
        URL_CHANGE: 'URL_CHANGE',
        POSITION_CHANGE: 'POSITION_CHANGE',
        OUTSIDE_MOUSE_EVENT: 'OUTSIDE_MOUSE_EVENT'
    };

    var Functions = {
        API_INIT: 'API_INIT',
        SET_PROPERTIES: 'SET_PROPERTIES',
        GET_PROPERTIES: 'GET_PROPERTIES',
        SET_SETTINGS: 'SET_SETTINGS',
        GET_SETTINGS: 'GET_SETTINGS',
        RELOAD_APP: 'RELOAD_APP',
        GET_PAGES: 'GET_PAGES',
        SET_CALL_HANDLER: 'SET_CALL_HANDLER',
        RESET_CALL_HANDLER: 'RESET_CALL_HANDLER',
        CALL_HANDLER: 'CALL_HANDLER',
        SET_DOCUMENT_SIZE: 'SET_DOCUMENT_SIZE',
        SET_APP_SIZE: 'SET_APP_SIZE',
        SET_APP_CONTENT_SIZE: 'SET_APP_CONTENT_SIZE',
        ENABLE_TOP_POSITION: 'ENABLE_TOP_POSITION',
        DISABLE_TOP_POSITION: 'DISABLE_TOP_POSITION',
        SET_HASH: 'SET_HASH'
    };

    var ApiType = {
        All: "all",
        App: "app",
        Manage: "manage"
    };

    var Managers = {
        EventManager: (new function() {
            var _eventsCallbacks = {};

            this.initialize = function() {
                cm.setCallHandler("events", eventHandler);
            };

            function eventHandler(data) {
                var eventName = data.eventName;

                var callbacks = _eventsCallbacks[eventName];
                if (!callbacks) return;
                for (var i = 0; i < callbacks.length; i++) {
                    callbacks[i](data.params);
                }
            }

            function removeLocalEventListener(eventName, callback) {
                var eventCallbacks = _eventsCallbacks[eventName];
                if (eventCallbacks) {
                    for(var i = 0; i < eventCallbacks.length; i++){
                        if(eventCallbacks[i] === callback){
                            eventCallbacks.splice(i, 1);
                            return;
                        }
                    }
                }
            }

            this.addEventListener = function(eventName, callback, oldEvents, validate) {
                if (validate !== false && !Events.hasOwnProperty(eventName)) {
                    return;
                }
                removeLocalEventListener(eventName, callback);
                var callbacks = _eventsCallbacks[eventName];
                if (!callbacks) {
                    callbacks = _eventsCallbacks[eventName] = [];
                }
                callbacks.push(callback);
            };

            this.removeEventListener = function(eventName, callback) {
                if (!Events.hasOwnProperty(eventName)) {
                    return;
                }
                var eventCallbacks = _eventsCallbacks[eventName];
                if (eventCallbacks) {
                    removeLocalEventListener(eventName, callback);
                    if (eventCallbacks.length === 0) {
                        delete _eventsCallbacks[eventName];
                    }
                }
            }
        })

    };

    // iframe manage
    var cm = new (function () {
        var _callbackId = 1,
            _id,
            _callbacks = {},
            _callsHandler = {},
            _queryParams,
            _viewMode,
            _apiType;

        function addMessageListener() {
            var receiver = receiveMessage;
            Utils.addEvent(window, "message", receiver);
        }

        this.initialize = function() {
            _queryParams = Utils.parseQueryParams();
            var encodeParams = _queryParams.wzEncodeParams;
            if (encodeParams) {
                var decoded = Utils.Base64.decode(encodeParams);

                try {
                    var params = JSON.parse(decoded);
                    for (var p in params) {
                        if (!_queryParams.hasOwnProperty(p)) {
                            _queryParams[p] = params[p];
                        }
                    }
                } catch(e) {
                    return;
                }
            }

            _id = _queryParams['wzId'];
            addMessageListener();
            var queryApiType = _queryParams['wzApiType'];
            _apiType = queryApiType ? (queryApiType === ApiType.App ? ApiType.App : ApiType.Manage) : ApiType.All;
            _viewMode = _queryParams['wzMode'];
            this.callFunction(Functions.API_INIT, { version: 1.0, type: _apiType});
        };

        this.getQueryParamValue = function(param) {
            return _queryParams[param];
        };

        this.getId = function() {
            return _id;
        };

        this.getApiType = function() {
            return _apiType;
        };

        this.getViewMode = function() {
            return _viewMode;
        };

        function getCallbackId() { return _callbackId++; }

        this.setCallHandler = function(callName, callback, options) {
            options = Utils.extend({
                oldCalls: false,
                isInvokeSupported: false
            }, options);
            var callHandler = _callsHandler[callName] = {
                callback: callback,
                isInvokeSupported: options.isInvokeSupported
            };
            this.callFunction(Functions.SET_CALL_HANDLER, { callName: callName, oldCalls: options.oldCalls });
            return callHandler;
        };

        this.callHandlerInvoke = function(callName, params, callback) {
            var callHandler = _callsHandler[callName];
            if (callHandler && callHandler.isInvokeSupported) {
                this.callFunction(Functions.CALL_HANDLER, { callName: callName, params: params }, callback);
            }
        };

        this.resetCallHandler = function(callName) {
            delete _callsHandler[callName];
        };

        function callHandler(data) {
            var callHandler = _callsHandler[data.callName];
            if (callHandler) {
                var result = callHandler.callback(data.content, data.oldCall);
                if (data.callbackId) {
                    sendResponse(data.callbackId, result);
                }

            }
        }

        function sendResponse(callbackId, content) {
            sendMessage("response", content, undefined, { callbackId: callbackId });
        }

        function receiveMessage(event)
        {
            if (!event || !event.data) {
                return;
            }

            // TODO: check origin

            var data;
            try {
                data = JSON.parse(event.data);
            } catch(e) {
                return;
            }
            switch(data.type) {
                case "response":
                    var callbackId = data.callbackId;
                    if (callbackId && _callbacks[callbackId]) {
                        _callbacks[callbackId](data.content);
                        delete _callbacks[callbackId];
                    }
                    break;
                case "call":
                    callHandler(data);
                    break;
            }
        }

        this.callFunction = function(funcName, params, callback) {
            sendMessage("call", { funcName: funcName, params: params }, callback);
        };

        function sendMessage(messageType, content, callback, params) {
            var target = parent.postMessage ? parent : (parent.document.postMessage ? parent.document : undefined);
            if (target) {

                var message = {
                    id: _id,
                    content: content,
                    type: messageType
                };
                if (callback) {
                    var callbackId = getCallbackId();
                    message.callbackId = callbackId;
                    _callbacks[callbackId] = callback;
                }
                if (params)
                    Utils.extend(message, params);
                target.postMessage(JSON.stringify(message), "*");
            }
        }
    });

    cm.initialize();

    initializeApi(cm.getApiType(), cm.getViewMode());


    function initializeCommonApi(viewMode) {
        Managers.EventManager.initialize();

        _wz.addEventListener = function(eventName, callback) {
            Managers.EventManager.addEventListener(eventName, callback);
        };
        _wz.removeEventListener = function(eventName, callback) {
            Managers.EventManager.removeEventListener(eventName, callback);
        };
        _wz.setProperties = function(key, value) {
            var obj = key;
            if (typeof key === 'string') {
                obj = {};
                obj[key] = value;
            }
            if (typeof obj === 'object') {
                cm.callFunction(Functions.SET_PROPERTIES, obj);
            }
        };
        _wz.getProperties = function(key, callback) {
            var obj;
            if (!key) {
                obj = 'all';
            } else if (typeof key === 'string') {
                obj = [key];
            }
            if (Utils.isArray(obj) || obj === 'all') {
                cm.callFunction(Functions.GET_PROPERTIES, obj, callback);
            }
        };
        _wz.setSettings = function(key, value) {
            var obj = key;
            if (typeof key === 'string') {
                obj = {};
                obj[key] = value;
            }
            if (typeof obj === 'object') {
                cm.callFunction(Functions.SET_SETTINGS, obj);
            }
        };
        _wz.setHash = function(hash) {
            if (typeof hash === 'string') {
                cm.callFunction(Functions.SET_HASH, {
                    hash: hash,
                    replace: false
                });
            }
        };
        _wz.replaceHash = function(hash) {
            if (typeof hash === 'string') {
                cm.callFunction(Functions.SET_HASH, {
                    hash: hash,
                    replace: true
                });
            }
        };
        _wz.getSettings = function(key, callback) {
            var obj;
            if (!key) {
                obj = 'all';
            } else if (typeof key === 'string') {
                obj = [key];
            } else {
                obj = key;
            }
            if (Utils.isArray(obj) || obj === 'all') {
                cm.callFunction(Functions.GET_SETTINGS, obj, callback);
            }
        };
        _wz.reloadApp = function(queryParams) {
            cm.callFunction(Functions.RELOAD_APP, queryParams);
        };
        _wz.getPages = function(callback) {
            cm.callFunction(Functions.GET_PAGES, undefined, callback);
        };
        _wz.getViewMode = function() {
            return cm.getViewMode();
        };
        _wz.getDeviceType = function() {
            return cm.getQueryParamValue('wzDeviceType');
        };
        _wz.getId = function() {
            return cm.getId();
        };
        _wz.getInstanceId = function() {
            return cm.getQueryParamValue('wzInstanceId');
        };
        _wz.getQueryParamValue = cm.getQueryParamValue;
    }

    function initializeAppApiViewAndPreview(apiType) {
        _wz.enableTopPosition = function() {
            cm.callFunction(Functions.ENABLE_TOP_POSITION);
        };
        _wz.disableTopPosition = function() {
            cm.callFunction(Functions.DISABLE_TOP_POSITION);
        };
    }

    function initializeAppApi(apiType, viewMode) {
        if (viewMode !== "designer") {
            initializeAppApiViewAndPreview(apiType);
        }

        _wz.setDocumentSize = function(size) {
            cm.callFunction(Functions.SET_DOCUMENT_SIZE, size);
        };

        _wz.setAppSize = function(size) {
            cm.callFunction(Functions.SET_APP_SIZE, size);
        };

        _wz.setAppContentSize = function(size) {
            cm.callFunction(Functions.SET_APP_CONTENT_SIZE, size);
        };

        _wz.getCurrentUrl = function() {
            return cm.getQueryParamValue("wzCurrentUrl") || '';
        };

        _wz.getCurrentUri = function() {
            var href = cm.getQueryParamValue("wzCurrentUrl");
            var uri;
            if (href) {
                uri = href.split("#")[0];
            }
            return uri || '';
        };

        _wz.getCurrentHash = function() {
            var href = cm.getQueryParamValue("wzCurrentUrl");
            var hash;
            if (href) {
                hash = href.split("#")[1];
            }
            return hash || '';
        };

        _wz.Features = {
            Implement: new (function () {
                var _features = {};
                var FEATURE_IMPLEMENT_PREFIX = "FeatureImpl.";
                this.addFeature = function (featureName, callback) {
                    var feature = {
                        callback: function (data, oldCall) {
                            return callback(data.functionName, data.params, data.callInfo, oldCall);
                        }
                    };
                    _features[featureName] = feature;
                    cm.setCallHandler(FEATURE_IMPLEMENT_PREFIX + featureName, feature.callback, {
                        oldCalls: true,
                        isInvokeSupported: true
                    });
                };
                this.fireEvent = function (featureName, eventName, params) {
                    var feature = _features[featureName];
                    if (feature) {
                        cm.callHandlerInvoke(FEATURE_IMPLEMENT_PREFIX + featureName, { eventName: eventName, params: params});
                    }
                }
            }),
            Connect: new (function () {
                var _features = {};
                var FEATURE_CONNECT_PREFIX = "FeatureConnect.";
                this.connectToFeature = function (featureName, callback) {
                    var feature = {
                        callback: function (data, oldCall) {
                            return callback(data.eventName, data.params, data.callInfo, oldCall);
                        }
                    };
                    _features[featureName] = feature;
                    cm.setCallHandler(FEATURE_CONNECT_PREFIX + featureName, feature.callback, {
                        oldCalls: true,
                        isInvokeSupported: true
                    });
                };
                this.callFunction = function (featureName, functionName, params, callback) {
                    var feature = _features[featureName];
                    if (feature) {
                        cm.callHandlerInvoke(FEATURE_CONNECT_PREFIX + featureName, { functionName: functionName, params: params}, callback);
                    }
                }
            })
        }
    }

    function initializeApi(apiType, viewMode) {
        initializeCommonApi(viewMode);
        if (apiType === ApiType.App || apiType === ApiType.All) {
            initializeAppApi(apiType, viewMode);
        }
    }
})();

