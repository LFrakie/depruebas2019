/*global WebFont*/
(function (global) {
    'use strict';

    function isLoadedInsideEditor() {
        var isEditor = false;

        //Try catch because it throws error when LPS is loaded in an iframe in external website (for instance Facebook)
        try {
            isEditor = !!document.querySelector('.EDITOR') || !!window.parent.editorInstance;
        } catch (ex) {

        }

        return isEditor;
    }

    (function () {
        'use strict';
        var ELEMENT = Element.prototype,
            HTML_ELEMENT = HTMLElement.prototype,
            insertAdjacentElement;

        insertAdjacentElement = function (where, content) {
            switch (where) {
                case 'afterBegin':
                    this.insertBefore(content, this.firstChild);
                    break;
                case 'beforeBegin':
                    this.parentNode.insertBefore(content, this);
                    break;
                case 'afterEnd':
                    this.parentNode.insertBefore(content, this.nextSibling);
                    break;
                default:
                    this.appendChild(content);
                    break;
            }
        };

        try {
            if (!ELEMENT.insertAdjacentHTML && !HTML_ELEMENT.insertAdjacentHTML) {
                ELEMENT.insertAdjacentHTML = function (where, html) {
                    var range,
                        frg;

                    range = document.createRange();
                    frg = range.createContextualFragment(html);

                    insertAdjacentElement.call(this, where, frg);
                };
            }
            //ie8+
            if (!ELEMENT.insertAdjacentElement && !HTML_ELEMENT.insertAdjacentElement) {
                ELEMENT.insertAdjacentElement = insertAdjacentElement;
            }

            //FF48+
            if (!ELEMENT.insertAdjacentText && !HTML_ELEMENT.insertAdjacentText) {
                ELEMENT.insertAdjacentText = function (where, text) {
                    var textNodeToInsert = document.createTextNode(text);

                    insertAdjacentElement.call(this, where, textNodeToInsert);
                };
            }
        } catch (ex) {
            alert(ex);
        }

        //IE11+
        if (undefined === document.documentElement.hidden) {
            try {
                Object.defineProperty(HTMLElement.prototype, 'hidden', {
                    set: function (value) {
                        if (/true/i.test(value)) {
                            this.setAttribute('hidden', 'true');
                        } else {
                            this.removeAttribute('hidden');
                        }
                    },
                    get: function () {
                        return 'true' === this.getAttribute('hidden', 2);
                    }
                });
            } catch (ex) {}
        }

        //IE11+
        if ('function' !== typeof Object.create) {
            Object.create = function (proto) {
                function F() {}
                F.prototype = proto;
                return new F();
            };
        }

        //IE11+
        if ('function' !== Object.keys) {
            Object.keys = function (o) {
                var ret = [], k;
                for (k in o) {
                    if (Object.prototype.hasOwnProperty.call(o, k)) {
                        ret.push(k);
                    }
                }
                return ret;
            };
        }

        //Custom GR
        Function.prototype.implement = function (propertyName, propertyValue) {
            if (!this.prototype[propertyName]) {
                this.prototype[propertyName] = propertyValue;
            }
        };

        //Custom GR
        Function.prototype.augment = function (propertyName, propertyValue) {
            if (!this[propertyName]) {
                this[propertyName] = propertyValue;
            }
        };

        //Custom GR
        Function.prototype.assign = Function.prototype.augment;

        //IE10+
        Function.implement('bind', function (context) {
            var that = this,
                args = Array.prototype.slice.call(arguments, 1);

            return function () {
                return that.apply(context, args.concat(Array.prototype.call(arguments)));
            };
        });

        //IE11+
        Array.augment('isArray', function (o) {
            return Object.prototype.toString.call(o) === '[object Array]' || (o instanceof Array);
        });

        //IE11+
        Array.implement('indexOf', function (item, start) {
            var i = 0,
                ln = this.length;
            start = start || 0;
            for (i = start; i < ln; i += 1) {
                if (this[i] === item) {
                    return i;
                }
            }
            return -1;
        });

        //IE11+
        Array.implement('forEach',function (fn, bind) {
            var i,
                l;
            if ('function' !== typeof fn) {
                throw new TypeError();
            }

            for (i = 0, l = this.length; i < l; i += 1) {
                if (this[i]) {
                    fn.call(bind, this[i], i, this);
                }
            }
        });

        //CUSTOM GR
        Array.implement('contains', function (item) {
            return -1 < Array.prototype.indexOf.call(this, item);
        });

        //Jest już modul require
        window.GR_COOKIE = (function () {
            function Cookie() {
            }
            Cookie.prototype = {
                write: function (name, value, days) {
                    var expires = '',
                        date;

                    if (days) {
                        date = new Date();
                        date.setTime(date.getTime() + (days * 24 * 60 * 60 * 1000));
                        expires = '; expires=' + date.toGMTString();
                    }

                    document.cookie = name + '=' + value + expires + '; path=/';
                },
                read: function (name) {
                    var regexp = new RegExp("(?:;)?" + name + "=([^;]*);?");
                    if (regexp.test(document.cookie)) {
                        return decodeURIComponent(RegExp.$1);
                    }
                    return null;
                },
                erase: function (name) {
                    this.write(name, '', -1);
                }
            };

            return new Cookie();
        }());
    }());

    window.APP = window.APP || function () {};

    (function () {
        'use strict';
        var SLICE = Array.prototype.slice,
            editor;

        var register = {};

        if (!global.APP.setItem) {
            APP.setItem = function(key, data) {
                register[key] = data;
            };
        }

        if (!global.APP.getItem) {
            APP.getItem = function(key) {
                return register[key];
            };
        }

        if (!global.APP.extend) {
            APP.extend = function () {
                var args = SLICE.call(arguments),
                    ob = args[0],
                    ln,
                    i,
                    prop;

                if ('boolean' === typeof ob) {
                    ob = args[1];
                } else {
                    args = SLICE.call(arguments, 1);
                }

                ob = ob || {};

                for (i = 0, ln = args.length; i < ln; i += 1) {
                    for (prop in args[i]) {
                        if (Object.prototype.hasOwnProperty.call(args[i], prop)) {
                            ob[prop] = args[i][prop];
                        }
                    }
                }
                return ob;
            };
        }

        APP.utils = {
            /*require: function ixRequire(src, callback) {
             var args = SLICE.call(arguments),
             toHEAD = true;

             if (args[0] && 'object' === typeof args[0]) {
             src = args[0].src;
             callback = args[0].callback;
             toHEAD = args[0].toHEAD || toHEAD;
             }

             if (loaded.contains(src)) {
             if (callback) {
             callback();
             }
             }
             },*/
            require: function (callback) {
                var script = document.createElement('script'),
                    s = document.head.getElementsByTagName('script');
                script.src = ('https:' === document.location.protocol ? 'https' : 'http') + '://ajax.googleapis.com/ajax/libs/webfont/1/webfont.js';
                script.type = 'text/javascript';
                script.async = 'true';
                s[s.length - 1].parentNode.insertBefore(script, s[s.length - 1]);

                script[(null === script.onreadystatechange ? 'onreadystatechange' : 'onload')] = function () {
                    if (this.readyState && !(/loaded|complete/.test(this.readyState))) {
                        return;
                    }

                    callback();
                };
            },
            socialrequire: function (opt) {
                try {
                    var t,
                        js,
                        doc = opt.doc || document,
                        elementName = opt.elementName || 'script',
                        script = doc.getElementsByTagName(elementName)[0];

                    if (Array.isArray(opt)) {
                        opt.forEach(function (el) {
                            APP.utils.socialrequire(el);
                        });
                        return;
                    }

                    if (doc.getElementById(opt.id)) {
                        return;
                    }

                    js = doc.createElement(elementName);
                    js.id = opt.id;
                    js.src = opt.src;
                    script.parentNode.insertBefore(js, script);

                    if (opt.exports) {
                        global.APP[opt.exports] = global.APP[opt.exports] || (t = {
                                _e: [],
                                ready: function (f) {
                                    t._e.push(f);
                                }
                            });
                    }
                } catch (ex) {

                }
            }
        };

        global.APP = APP;

        global.WebFontConfig = {
            google: {
                families: []
            }
        };

        document.addEventListener("DOMContentLoaded", function() {
            var isEditor = isLoadedInsideEditor(),
                optElement = document.getElementById('OPT'),
                toEditor = [];

            if (isEditor) {
                //global.EDITOR = editor = global.parent.editor || global.opener.editor;
                if (global.parent.APP.FONTS) {
                    global.WebFontConfig.google.families = global.parent.APP.FONTS.WebFontConfig.google.families;
                }
            }

            if (optElement) {
                (function (form) {
                    var select = form.GoogleWebFont,
                        options,
                        ln,
                        i;

                    if (select) {
                        options = select.options;
                        ln = options.length;

                        for (i = 0; i < ln; i += 1) {
                            //Urle nie moga zawierac spacji w sobie, google zwraca blad w requescie i font nie jest zaciagniety
                            //Moze to byc spowodowane tym ze graficy zle je zdefiniowali
                            var fontDefinition = (options[i].text).replace(/\s/mg, '');

                            //Ludzie ktorzy tworzyli templaty nie trzymali sie zasad z guideline. Kopiowali to co google-font im dawal
                            //nie zwracajac uwagi na to ze 'subset' powinien byc na samym koncu.
                            //Czyli family='wszystkie definicje czionek'&subset='wszystkie ich odmiany'
                            fontDefinition = fontDefinition.replace('&subset=', ':');
                            fontDefinition = fontDefinition.replace(/(\"|\')/g, '');

                            if (!global.WebFontConfig.google.families.contains(fontDefinition)) {
                                global.WebFontConfig.google.families.push(fontDefinition);
                                toEditor.push(fontDefinition);
                            }
                        }
                    }
                }(optElement.getElementsByTagName('form')[0]));
            }

            APP.utils.require(function () {
                WebFont.load(global.WebFontConfig);
            });
        }, false);

    }());

    (function () {
        'use strict';
        var isEditor = false,
            helper = {
                on: function (element, type, fn) {
                    if (element.addEventListener) {
                        element.addEventListener(type, fn, false);
                    } else {
                        if (element.attachEvent) {
                            element.attachEvent('on' + type, fn);
                        }
                    }
                },
                off: function (element, type, fn) {
                    if (element.removeEventListener) {
                        element.removeEventListener(type, fn, false);
                    } else {
                        if (element.detachEvent) {
                            element.detachEvent('on' + type, fn);
                        }
                    }
                },
                cancelEvent: function (e) {
                    e = e ? e : window.event;
                    if (e.stopPropagation) {
                        e.stopPropagation();
                    }
                    if (e.preventDefault) {
                        e.preventDefault();
                    }
                    e.cancelBubble = true;
                    e.cancel = true;
                    e.returnValue = false;
                    return false;
                },
                parent: function (element, selector, cx) {
                    var all,
                        parent = element.parentNode;

                    if (selector) {
                        all = Array.prototype.slice.call((cx || element.ownerDocument).querySelectorAll(selector));

                        while (parent && !all.contains(parent)) {
                            parent = parent.parentNode;
                        }
                    }

                    return parent;
                }
            };

        helper.webform = function (webform) {
            var inputs = Array.prototype.slice.call(webform.querySelectorAll('input[type="text"], textarea')),
                ln = inputs.length,
                i;

            for (i = 0; i < ln; i += 1) {
                if (!inputs[i].labels) {
                    inputs[i].labels = webform.querySelectorAll('label[for="' + inputs[i].id + '"]');
                }

                inputs[i].disabled = false;

                helper.placeholder(webform, inputs[i]);
            }
        };

        // @todo tymczas - przerobic
        helper.placeholder = function (webform, input) {
            var isNewWebform = webform.getAttribute('data-editable') === 'webformNew' || webform.getAttribute('data-editable') === 'webinarNew';

            if (isNewWebform) {
                (function() {
                    if (webform.getAttribute('data-label-type') === 'placeholder') {
                        var inputId = (input.id || '').replace('-for', '');//Remove it
                        //Special case, 2 labels with the same uid and id (phone field) for the rest should be only one
                        var label, labelFields = Array.prototype.slice.call(webform.querySelectorAll('[data-editable-item="label"]#' + inputId));

                        for(var i = 0; i < labelFields.length; i++) {
                            var label = labelFields[i];

                            //There can be hidden label when phone-field is in placeholder mode
                            if (!$(label).is(':hidden')) {
                                helper.on(label, 'click', function (input, e) {
                                    input.focus();
                                }.bind(null, input));

                                helper.on(label, 'click', function(input, label, e) {
                                    label.style.display = 'none';
                                    input.focus();
                                }.bind(null, input, label));

                                helper.on(input, 'focus', function(input, label) {
                                    label.style.display = 'none';
                                }.bind(null, input, label));

                                helper.on(input, 'blur', function(input, label, e) {
                                    if (input.value.trim()) {
                                        return;
                                    }

                                    label.style.display = 'inline-block';
                                }.bind(null, input, label));

                                if (input.value) {
                                    label.style.display = 'none';
                                }
                            }
                        }
                    }
                }());
            } else {
                (function() {
                    var offsetParent = helper.parent(input, 'div.type-placeholder'),
                        label,
                        parent;

                    if (!offsetParent) {
                        return;
                    }

                    if (input.labels && input.labels[0]) {
                        label = input.labels[0];
                    } else {
                        label = offsetParent.getElementsByTagName('label')[0];
                    }

                    parent = label.parentNode;

                    if (parent) {
                        if (!input.value.trim()) {
                            parent.style.display = 'block';
                        }

                        parent.style.width = input.offsetWidth + 'px';
                        //Ustaw wysokosc div.label aby wypozyjonowal sie odpowiednio wzgledem inputa
                        parent.style.height = input.offsetHeight + 'px';
                    }

                    helper.on(input, 'focus', function () {
                        try {
                            parent.style.display = 'none';
                        } catch (ex) {

                        }
                    });

                    helper.on(input, 'blur', function () {
                        if (input.value.trim()) {
                            return;
                        }
                        try {
                            parent.style.display = 'block';
                        } catch (ex) {

                        }
                    });
                }());
            }
        };
        document.addEventListener('DOMContentLoaded', function () {
            var ed = (global && global.parent),
                div;

            isEditor = isLoadedInsideEditor();

            div = document.getElementsByClassName('.backdrop')[0];

            if (!isEditor) {
                if (div) {
                    div.parentNode.removeChild(div);
                }

                (function (webforms) {
                    var ln,
                        i;

                    webforms = Array.prototype.slice.call(webforms);

                    ln = webforms.length;

                    for (i = 0; i < ln; i += 1) {
                        helper.webform(webforms[i]);
                    }
                }(document.body.querySelectorAll('[data-editable="webform"], [data-editable="webformNew"], [data-editable="webinarNew"]')));

                return;
            }

            if (!div) {
                div = (function () {
                    var el = document.createElement('div');
                    el.className = 'backdrop';
                    document.body.insertAdjacentElement('afterBegin', el);
                    return el;
                }());
            }

            div.hidden = true;
        }, false);
    }());

    (function () {
        'use strict';
        var APP = global.APP;
        APP.io = (function () {
            var hasOwn = Object.prototype.hasOwnProperty,
                slice = Array.prototype.slice,
                events = ['abort', 'error', 'load', 'loadend', 'loadstart', 'progress', 'readystatechange', 'timeout'],
                ln,
                i;

            var class2type = {};
            var toString = class2type.toString;
            var fnToString = class2type.hasOwnProperty.toString;
            var hasOwn = class2type.hasOwnProperty;
            var ObjectFunctionString = fnToString.call( Object );
            var rbracket = /\[\]$/;

            // Populate the class2type map
            "Boolean Number String Function Array Date RegExp Object Error Symbol".split(" ").forEach(function(name, index) {
                class2type[ "[object " + name + "]" ] = name.toLowerCase();
            });

            //can be deleted when jQuery is loaded
            var jQueryAjax = {
                type: function( obj ) {
                    if ( obj == null ) {
                        return obj + "";
                    }

                    // Support: Android <=2.3 only (functionish RegExp)
                    return typeof obj === "object" || typeof obj === "function"
                        ? class2type[ toString.call( obj ) ] || "object"
                        : typeof obj;
                },
                isFunction: function( obj ) {
                    return this.type( obj ) === "function";
                },

                isArray: Array.isArray,

                isPlainObject: function( obj ) {
                    var proto, Ctor;

                    // Detect obvious negatives
                    // Use toString instead of jQuery.type to catch host objects
                    if ( !obj || toString.call( obj ) !== "[object Object]" ) {
                        return false;
                    }

                    proto = Object.getPrototypeOf( obj );

                    // Objects with no prototype (e.g., `Object.create( null )`) are plain
                    if ( !proto ) {
                        return true;
                    }

                    // Objects with prototype are plain iff they were constructed by a global Object function
                    Ctor = hasOwn.call( proto, "constructor" ) && proto.constructor;
                    return typeof Ctor === "function" && fnToString.call( Ctor ) === ObjectFunctionString;
                },

                buildParams: function( prefix, obj, traditional, add ) {
                    var name;
                    var _self = this;

                    if (this.isArray(obj)) {
                        // Serialize array item.
                        obj.forEach(function( value, index ) {
                            if ( traditional || rbracket.test( prefix ) ) {
                                // Treat each array item as a scalar.
                                add( prefix, value );
                            } else {
                                // Item is non-scalar (array or object), encode its numeric index.
                                _self.buildParams(
                                    prefix + "[" + ( typeof value === "object" && value != null ? index : "" ) + "]",
                                    value,
                                    traditional,
                                    add
                                );
                            }
                        } );

                    } else if ( !traditional && this.type( obj ) === "object" ) {

                        // Serialize object item.
                        for ( name in obj ) {
                            this.buildParams( prefix + "[" + name + "]", obj[ name ], traditional, add );
                        }

                    } else {

                        // Serialize scalar item.
                        add( prefix, obj );
                    }
                },

                reformatObjects: function( data, traditional ) {
                    var _self = this;
                    var prefix,
                        s = [],
                        add = function( key, valueOrFunction ) {
                            // If value is a function, invoke it and use its return value
                            var value = _self.isFunction( valueOrFunction ) ?
                                valueOrFunction() :
                                valueOrFunction;

                            s[ s.length ] = encodeURIComponent( key ) + "=" +
                                encodeURIComponent( value == null ? "" : value );
                        };

                    // If an array was passed in, assume that it is an array of form elements.
                    if ( this.isArray( data ) || ( data.jquery && !this.isPlainObject( data ) ) ) {

                        // Serialize the form elements
                        for(var keyName in data) {
                            if (data.hasOwnProperty(keyName)) {
                                add( keyName, data[keyName] );
                            }
                        }

                    } else {

                        // If traditional, encode the "old" way (the way 1.3.2 or older
                        // did it), otherwise encode params recursively.
                        for ( prefix in data ) {
                            this.buildParams( prefix, data[ prefix ], traditional, add );
                        }
                    }

                    // Return the resulting serialization
                    return s.join( "&" );
                }
            };

            function prepareParams(data) {
                var ret,
                    k;
                if (!data) {
                    return '';
                }
                ret = [];
                for (k in data) {
                    if (hasOwn.call(data, k)) {
                        ret.push(k + '=' + encodeURIComponent(data[k]));
                    }
                }
                return ret.join('&');
            }

            function xhr(desc, useStringify) {
                var ob = Object.create(xhr.prototype),
                    con = new XMLHttpRequest();

                APP.extend(ob, {
                    xhr: con
                }, desc);

                if (ob.onstart) {
                    ob.onstart();
                }

                /*con.onreadystatechange = function () {
                 log(this.readyState);
                 };*/

                ob.setEventsListeners();

                ob.send(useStringify);

                return con;
            }

            function prepareEvent(callback, dataType) {
                return function () {
                    var response = 'xml' !== dataType ? this.responseText : this.responseXML;

                    if (response && 'json' === dataType) {
                        response = JSON.parse(response);
                    }

                    callback.call(this, response, this);
                };
            }

            xhr.prototype = Object.create({
                setEventsListeners: function () {
                    var oThis = this;

                    if (this.statusCode) {
                        this.xhr.addEventListener('loadend', prepareEvent(function () {
                            if (oThis.statusCode[this.status]) {
                                oThis.statusCode[this.status].apply(this, slice.call(arguments));
                            }
                        }, oThis.dataType), false);
                    }

                    for (i = 0, ln = events.length; i < ln; i += 1) {
                        if (!this.xhr.hasOwnProperty(events[i]) && !this['on' + events[i]]) {
                            continue;
                        }
                        this.xhr.addEventListener(events[i], prepareEvent(oThis['on' + events[i]], oThis.dataType), false);
                    }

                    if (this.onend) {
                        this.xhr.addEventListener('loadend', this.onend, false);
                    }
                },
                setHeaders: function () {
                    var k;

                    this.headers['X-Requested-With'] = 'XMLHttpRequest';

                    if (/post/i.test(this.type)) {
                        this.headers['Method'] = 'POST ' + this.url + ' HTTP/1.1';
                        this.headers['Content-type'] = 'application/x-www-form-urlencoded; charset=utf-8';
                    }

                    for (k in this.headers) {
                        if (hasOwn.call(this.headers, k)) {
                            this.xhr.setRequestHeader(k, this.headers[k]);
                        }
                    }
                },
                send: function (useStringify) {
                    var params = useStringify ? jQueryAjax.reformatObjects(this.data) : prepareParams(this.data);

                    this.xhr.open(this.type, this.url, this.async || true, this.username, this.password);

                    this.setHeaders();

                    this.xhr.send(params);
                }
            });

            APP.extend(xhr.prototype, {
                async: true,
                dataType: 'json',
                contentType: 'application/x-www-form-urlencoded; charset=UTF-8',
                context: null,
                data: null,
                headers: {},
                statusCode: null,
                type: 'GET',
                url: '',
                username: undefined,
                password: undefined,
                timeout: 0,

                onabort: null,
                onerror: null,
                onload: null,
                onloadend: null,
                onloadstart: null,
                onprogress: null,
                onreadystatechange: null,
                ontimeout: null,

                onstart: null,
                onend: null,
                withCredentials: false
            });

            xhr.get = function (url) {
                var args = slice.call(arguments, 1),
                    onload = args.pop(),
                    data = args.pop();

                return xhr({
                    url: url,
                    onload: onload,
                    data: prepareParams(data)
                });
            };

            xhr.post = function (url, data, onload, useStringify) {
                /*var args = slice.call(arguments, 1),
                 onload = args.pop(),
                 data = args.pop();*/

                return xhr({
                    url: url,
                    type: 'POST',
                    onload: onload,
                    data: data
                }, useStringify);
            };

            return {
                xhr: xhr,
                get: xhr.get,
                post: xhr.post
            };
        }());
    }());

    (function () {
        'use strict';
        var PAGE_URL = document.location.href,
            PROTOCOL = document.location.protocol,
            REQ = {
                facebook: {
                    id: 'facebook-jssdk',
                    src: '//connect.facebook.net/en_US/all.js#xfbml=1'
                },
                tweet: {
                    id: 'twitter-wjs',
                    src: '//platform.twitter.com/widgets.js',
                    exports: 'twttr'
                },
                pinterest: {
                    id: 'pinterest',
                    src: '//assets.pinterest.com/js/pinit_main.js'
                },
                linkedin: {
                    id: 'linkedin',
                    src: '//platform.linkedin.com/in.js'
                },
                googleplus: {
                    id: 'googleplus',
                    src: 'https://apis.google.com/js/plusone.js'
                }
            },
            INS = {
                facebook: '<div class="fb-like" data-href="' + PAGE_URL + '" data-width="450" data-action="like" data-width="90" data-share="false" data-layout="button_count" data-show-faces="false"></div>',
                tweet: '<a href="https://twitter.com/share" class="twitter-share-button" data-url="' + PAGE_URL + '">Tweet</a>',
                pinterest: '<a href="' + PROTOCOL + '//pinterest.com/pin/create/button/?url=' + PAGE_URL + '&amp;media=&amp;description=" data-pin-zero="true" data-pin-do="buttonPin" data-pin-config="beside">'
                + '<img border="0" src="//assets.pinterest.com/images/PinExt.png" title="Pin It">'
                + '</a>',
                googleplus: '<div class="g-plus" data-action="share" data-annotation="bubble" data-href="' + PAGE_URL + '"></div>',
                linkedin: '<script type="IN/Share" data-counter="right" data-showzero="true" data-url="' + PAGE_URL + '"></script>',
                none: ''
            };

        document.addEventListener('DOMContentLoaded', function () {
            var IS_EDITOR = isLoadedInsideEditor();

            if (window.clear) {
                window.clear();
            }

            if (IS_EDITOR) {
                window.APP.utils.socialrequire([{
                    id: 'facebook-jssdk',
                    src: '//connect.facebook.net/en_US/all.js#xfbml=1'
                }, {
                    id: 'twitter-wjs',
                    src: '//platform.twitter.com/widgets.js',
                    exports: 'twttr'
                }, {
                    id: 'pinterest',
                    src: '//assets.pinterest.com/js/pinit_main.js'
                }, {
                    id: 'googleplus',
                    src: 'https://apis.google.com/js/plusone.js'
                }, {
                    id: 'linkedin',
                    src: '//platform.linkedin.com/in.js'
                }]);
            }

            // social
            (function (ch) {
                var div = document.createElement('div'),
                    req = [],
                    re,
                    type,
                    ln,
                    i;
                ch = Array.prototype.slice.call(ch);

                for (i = 0, ln = ch.length; i < ln; i += 1) {
                    re = ch[i].textContent.match(/\[\[([^:]+?)\]\]/);
                    type = ch[i].getAttribute('data-type', 2) || (re && re[1]) || 'none';

                    if (type) {
                        if (!req.contains(REQ[type])) {
                            req.push(REQ[type]);
                        }

                        ch[i].innerHTML = '';

                        div.innerHTML = INS[type];

                        if (div.children) {
                            if (0 < div.children.length) {
                                ch[i].appendChild(div.children[0]);
                            }
                        }
                    }
                }

                window.APP.utils.socialrequire(req);
            }(document.querySelectorAll('[data-editable="social"]')));

            if (window.fbAsyncInit) {
                (function (links) {
                    var ln = links.length,
                        i;

                    for (i = 0; i < ln; i += 1) {
                        links[i].target = '_blank';
                    }
                }(Array.prototype.slice.call(document.links)));
            }

            (function () {
                var cookieNotificationBox = document.querySelector('[data-editable="boxCookieNotification"]');

                if (!cookieNotificationBox) {
                    return;
                }

                if (!IS_EDITOR && window.GR_COOKIE.read('gr_cookie_notification_clicked')) {
                    cookieNotificationBox.parentNode.removeChild(cookieNotificationBox);
                    return;
                }

                cookieNotificationBox.style.display = 'block';

                document.querySelector('[data-editable="buttonCookieNotification"] a').addEventListener('click', function (e) {
                    var href = this.getAttribute('href');
                    if (!href || '#' === href) {
                        e.preventDefault();
                        e.stopPropagation();
                    }

                    window.GR_COOKIE.write('gr_cookie_notification_clicked', true, 999999);

                    cookieNotificationBox.parentNode.removeChild(cookieNotificationBox);
                }, false);
            }());

            (function() {
                var initialData = APP.initialData;

                //Handle Google Tag Manager
                if (IS_EDITOR || !initialData || initialData.tracking_code_type !== 'google_tag_manager') {
                    return;
                }

                var script = document.createElement('script');

                script.innerHTML =
                    "dataLayer = [];" +
                    "(function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({" +
                    "'gtm.start':new Date().getTime(),event:'gtm.js'});" +
                    "var f=d.getElementsByTagName(s)[0],j=d.createElement(s),dl=l!='dataLayer'?'&amp;l='+l:'';" +
                    "j.async=true;j.src='//www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);})(window,document,'script','dataLayer', '" + initialData.tracking_code_id + "');" +
                    "";

                document.body.insertAdjacentElement('afterbegin', script);
            }());

            //Handle webinars
            (function() {
                if (IS_EDITOR) {
                    return;
                }

                //@see editable/webinars.js because following code is a copy of it
                function getWebinar(id) {
                    var webinarsList = APP.getItem('webinars').list,
                        i, l = webinarsList.length;

                    for (i = 0; i < l; i++) {
                        if (webinarsList[i].id === Number(id)) {
                            return webinarsList[i];
                        }
                    }

                    return null;
                }

                function setWebinarData(elWebinar, webinar) {
                    var matchwords = Array.prototype.slice.call(elWebinar.querySelectorAll('[data-webinar-item]'));

                    elWebinar.classList.remove('no-webinar');

                    //fill out with data only when webinar exist, otherwise show old content
                    if (webinar) {
                        matchwords.forEach(function (el, index) {
                            var name = el.getAttribute('data-webinar-item'),
                                value = webinar[name] || '';

                            if (name === 'room_link') {
                                el.href = value;
                            } else {
                                el.innerHTML = value;
                            }
                        });
                    }
                }

                function fakeTabComponent(el, webinar, index) {
                    var tabPanels = Array.prototype.slice.call(el.querySelectorAll('[role="tabpanel"]'));

                    for(var i = 0; i < tabPanels.length; i++) {
                        tabPanels[i].setAttribute('aria-hidden', true);
                    }

                    tabPanels[index].removeAttribute('aria-hidden');
                }

                (function(webinars) {
                    webinars.forEach(function(el, idx) {
                        var webinarId = el.getAttribute('data-webinar-id');
                        var webinar = getWebinar(webinarId);
                        var index = 0;

                        fakeTabComponent(el, webinar, index);
                        setWebinarData(el, webinar);
                    });
                }(Array.prototype.slice.call(document.querySelectorAll('[data-editable="webinar"]'))));
            }());


            (function() {
                if (isLoadedInsideEditor()) {
                    return;
                }

                if (!APP.initialData.country_phone_codes) {//will be removed when we will have custom build for every page
                    return;
                }

                var phonePrefixData = APP.initialData.country_phone_codes;

                phonePrefixData.sort(function (a, b) {
                    if (a.cc === 'us') return -1;
                    if (b.cc === 'us') return 1;
                    if (a.name < b.name) return -1;
                    if (a.name > b.name) return 1;
                    return 0;
                });

                var phoneCustom = {
                    $body: null,
                    $curtain: null,
                    $forms: null,
                    $list: null,
                    opened: false,

                    initialize: function() {
                        this.$body = $(document.body);
                        this.$forms = this.$body.find('[data-editable="webformNew"], [data-editable="webinarNew"]');

                        //phonePrefixData
                        this.initializeEventListeners();
                    },

                    initializeEventListeners: function() {
                        var stashedValue = '';

                        this.$forms.on('click', '[data-feature-part="directional"]', function(e) {
                            var $directional = $(e.currentTarget);

                            if ($(e.target).parents('[data-editable-item="label"]').length) {
                                //Don't open the list when user clicked on the label
                                return;
                            }

                            var $editable = $directional.parents('[data-editable="webformNewItem"]');
                            var $form = $directional.parents('[data-editable="webformNew"], [data-editable="webinarNew"]');

                            this.toggleList($form, $editable);
                        }.bind(this));

                        if (!this.getAndroidVersion()) {

                            this.$forms.on('focus', '[data-editable="webformNewItem"][data-item-what="phone"] input', function(e) {
                                //Stash current value when input is focused
                                stashedValue = this.getPureNumber($(e.currentTarget).val());
                            }.bind(this));

                            this.$forms.on('keyup', '[data-editable="webformNewItem"][data-item-what="phone"] input', function(e) {
                                var $input = $(e.currentTarget);
                                var cc = $input.attr('data-country-code');
                                var format = this.getFormat(cc);

                                //Backspace or delete
                                if (e.keyCode === 8 || e.keyCode === 46) {
                                    stashedValue = stashedValue.substr(0, Math.min(stashedValue.length - 1, $input.val().length));
                                    //stashedValue = stashedValue.substr(0, $input.val().length);
                                } else if (e.keyCode === 13 || e.keyCode === 9) {
                                    //do nothing for enter and tab
                                }
                                else {
                                    if (e.key.length === 1) {
                                        stashedValue += e.key;
                                    }
                                }

                                //Dont allow to store more characters
                                var numberOfAllowedChars = format.replace(/\(|\)|-|\s/g, '').length || Infinity;//if length is 0, it means that there is no format prepared, take all characters
                                stashedValue = stashedValue.substr(0, numberOfAllowedChars);
                                var newValue = this.getFormattedNumber(stashedValue, format);

                                $input.val(newValue);

                                $input.parents('[data-editable-item="field"]').find('[data-label-type="placeholder"] [data-editable-item="label"]').toggle(newValue === '');
                            }.bind(this));
                        } else {
                            this.$forms.on('focusout', '[data-editable="webformNewItem"][data-item-what="phone"] input', function(e) {
                                var $input = $(e.currentTarget);
                                var cc = $input.attr('data-country-code');
                                var format = this.getFormat(cc);
                                var stashedValue = this.getPureNumber($(e.currentTarget).val());
                                var numberOfAllowedChars = format.replace(/\(|\)|-|\s/g, '').length || Infinity;//if length is 0, it means that there is no format prepared, take all characters
                                stashedValue = stashedValue.substr(0, numberOfAllowedChars);
                                var newValue = this.getFormattedNumber(stashedValue, format);

                                $input.val(newValue);

                                $input.parents('[data-editable-item="field"]').find('[data-label-type="placeholder"] [data-editable-item="label"]').toggle(newValue === '');
                            }.bind(this));

                        }

                    },

                    getAndroidVersion: function() {
                        var ua = navigator.userAgent.toLowerCase();
                        var match = ua.match(/android\s([0-9\.]*)/);
                        return match ? match[1] : false;
                    },

                    getPureNumber: function(number) {
                        return number.replace(/\(|\)|-|\s/g, '');
                    },

                    getFormat: function(cc) {
                        for(var i = 0; i < phonePrefixData.length; i++) {
                            if (phonePrefixData[i].cc === cc) {
                                return phonePrefixData[i].format || '';
                            }
                        }
                        return '';
                    },

                    toggleList: function($form, $editable) {
                        if (this.opened) {
                            this.hideList($editable);
                        } else {
                            this.buildList($form, $editable);
                        }
                    },

                    buildList: function($form, $editable) {
                        var str = '<div id="custom-phone-curtain"><div class="custom-phone-list-wrapper" data-feature-part="list-wrapper"><ul class="custom-phone-list">';
                        var $directional = $editable.find('[data-feature-part="directional"]');

                        var selectedCC = $directional.attr('data-country-code');

                        for(var i = 0; i < phonePrefixData.length; i++) {
                            var rowData = phonePrefixData[i];

                            str +=
                                '<li data-feature-part="list-item" data-country-code="' + rowData.cc + '" data-format="' + (rowData.format || '') + '" data-code="' + rowData.code + '" class="custom-phone-list-item ' + (selectedCC === rowData.cc ? 'selected' : '') + '">' +
                                    '<label>' +
                                        '<input type="radio" name="unique_name" value="' + rowData.cc + '">' +
                                        '<span class="custom-phone-list-item-label" data-flag="' + rowData.cc + '">' + rowData.name + ' (+' + rowData.code + ')</span>' +
                                    '</label>' +
                                '</li>';
                        }

                        str += '</div></div>';

                        this.$body.append(str);
                        this.$curtain = this.$body.find('#custom-phone-curtain');

                        this.$list = this.$curtain.find('[data-feature-part="list-wrapper"]');

                        var pos = $editable.offset();
                        var width = $editable.width();
                        var height = $editable.height();

                        $editable.addClass('opened');

                        this.$list.css({
                            top: pos.top + height,
                            left: pos.left,
                            width: width
                        });

                        this.$list.on('click', '[data-feature-part="list-item"]', function(e) {
                            var $item = $(e.currentTarget);
                            var format = $item.data('format');
                            var $input = $editable.find('[data-editable-item="field"] input');
                            var $directional = $editable.find('[data-feature-part="directional"] .directional');

                            var cc = $item.data('country-code');

                            $directional.find('[data-feature-part="directional-container"]').html('+' + $item.data('code'));
                            $directional.attr('data-country-code', cc);
                            $directional.attr('data-flag', cc);

                            $input.attr('data-country-code', cc);

                            $input.val(this.getFormattedNumber(
                                this.getPureNumber($input.val()),
                                this.getFormat(cc)
                            ));
                        }.bind(this));

                        this.$curtain.on('click', function() {
                            this.hideList($editable);
                        }.bind(this));

                        this.opened = true;
                    },

                    hideList: function($editable) {
                        this.$list.off();
                        this.$curtain.off().remove();
                        this.opened = false;

                        $editable.removeClass('opened');
                    },

                    getFormattedNumber: function (number, format) {
                        return this.formatNumber(this.clear(number), format);
                    },

                    formatNumber: function (number, format) {
                        var onlyFormatersChar = false;
                        if (!format) {
                            return number;
                        }
                        number = String(number || '').split('');
                        return format.replace(/./g, function (char) {
                            var ret = '';
                            if (!number.length) {
                                if (onlyFormatersChar && 'X' !== char) {
                                    return char;
                                }
                                onlyFormatersChar = false;
                                return '';
                            }
                            ret = char;
                            if ('X' === char) {
                                ret = number.shift();
                                if (!number.length) {
                                    onlyFormatersChar = true;
                                }

                            }
                            return ret;
                        });
                    },

                    clear: function (number) {
                        var stringToNumber = {
                            a: 2, b: 2, c: 2,
                            d: 3, e: 3, f: 3,
                            g: 4, h: 4, i: 4,
                            j: 5, k: 5, l: 5,
                            m: 6, n: 6, o: 6,
                            p: 7, q: 7, r: 7, s: 7,
                            t: 8, u: 8, v: 8,
                            w: 9, x: 9, y: 9, z: 9
                        };

                        return String(number).replace(/((\d)|(.))/g, function (all, char, number, otherChar) {
                            if (number) {
                                return number;
                            }
                            if (otherChar) {
                                if (/[a-zA-Z]/.test(otherChar)) {
                                    otherChar = otherChar.toLowerCase();
                                    return stringToNumber[otherChar] || '';
                                }
                                return '';
                            }
                        });
                    }
                };

                 phoneCustom.initialize();
             }());


            (function() {
                /**
                 * ===================
                 * Handle WF Z-indexes (only new WF)
                 * ===================
                 */
                var webformFields = Array.prototype.slice.call(document.querySelectorAll([
                    '[data-editable="webformNew"] [data-editable="webformNewItem"]',
                    '[data-editable="webformNew"] [data-editable="webformNewButton"]',
                    '[data-editable="webinarNew"] [data-editable="webformNewItem"]',
                    '[data-editable="webinarNew"] [data-editable="webformNewButton"]'
                ].join(', ')));

                //@todo This code is almost 1:1 as in arena_controller.js. Make a module when possible
                var sorted = webformFields.sort(function(a, b) {
                    var rectA = a.getBoundingClientRect();
                    var rectB = b.getBoundingClientRect();
                    var diff = rectA.top - rectB.top;

                    //When there are two elements with the same position, and one of it is a button, then give priority to the item
                    if (diff === 0) {
                        if (a.getAttribute('data-editable') === 'webformNewButton') {
                            return rectB.top + 1;
                        } else if (b.getAttribute('data-editable') === 'webformNewButton') {
                            return rectA.top + 1;
                        }
                    } else {
                        return diff;
                    }
                });

                var tabIndex = 1;
                sorted.forEach(function(fieldElement, index) {
                    var webformElements = Array.prototype.slice.call(fieldElement.querySelectorAll([
                        'input', 'button', 'select', 'textarea'
                    ].join(', ')));

                    webformElements.forEach(function(webformElement) {
                        webformElement.setAttribute('tabindex', tabIndex++);
                    });
                });
            }());
        });
    }());
}(this));