 ### 转自imdong,大佬的文笔很棒

先讲一下我所知道的 JS 加密 /混淆有哪些
其实我个人并不认为下面讲的这些是加密，顶多是混淆罢了，所以本文后续尽量不用加密来描述。

eval 加密
最常见的如 packer 等

典型特征
代码内使用 eval 方法，大致原理是对代码进行文本级别的抽取可复用参数进行压缩混淆。
而使用时将混淆代码还原为字符串，然后使用 eval 运行代码。

怎么破解
也许在 eval 之前的代码可能确实是用算法加密了，但毫无意义，因为总是要解密。
我们跳过他加解密的过程，直接在他最终执行代码的地方获取原文即可。
比如直接将 eval 改为 console.log 基本可直接输出代码原文，
如果不行或者找不到，则手动覆盖 eval 方法，然后执行代码，然后真正的代码就会从参数传进来。

变量混淆
例如接下来要讲的 Obfuscator(本文针对的 sojson 的混淆即由此衍生，或者是抄袭？)
还有所谓的二次元加密?

典型特征
没有特征，就是一堆看不懂名字的代码，
原理直白的说，就是把你用的变量名 /方法名替换成无规律的名称实现干扰。
例如，你可以认为很多代码压缩自带混淆功能，因为压缩以后人肉理解比较困难。

编译
还有一些基于编译性质的，了解不多，不做讨论。
我也是昨天发帖后才知道的。

正式开始
首先，我们需要几个工具：

一个代码编辑器，如：VS Code
Chrome 浏览器，用来调试代码
需要解密的 JS 文件，我们直接以这个 sojson.v5 为准了
JS 代码格式化工具，例如： https://tool.css-js.com/ 、http://www.bejson.com/jshtml_format/
一个聪明的脑瓜子，例如：你
格式化并整理代码
格式完的代码，我们会看到 Unicode 编码的数据已经自动转换回来了，
如果没有转换，可以自己考虑写代码转换，例如：

code_str.replace(/\\x([a-f0-9]{2})/g, function (match_str, group_1) {
    return String.fromCharCode(parseInt(group_1, 16).toString(10));
});
现在我们先逐段分析每一段代码，有些代码可能太长，会有部分截断。
我们在翻译代码的时候，会将一些变量给命名，建议使用前缀尽量不要和后面的代码有可能相同的变量名，
不然后续就很蛋疼了，并且重命名以后不要丢弃原来的变量名，指不定什么时候发现这个变量没替换到，
而你又不记得是什么地方来的。

这里，我们把变量名前缀定义为 qs_

然后我们文章内所有用到行数的，均以格式化后的完整代码为准(jiami_format.js)。

第一段 (定义了三个变量)
var encode_version = "sojson.v5",
    brfui = "",
    _0x2c67 = ["VHtkTcOA", "DcOPVEB/", "UMK...o9WPA=="];
这里定义了三个变量，第一个是作者的版权，第二个暂时不知道，第三个是一个 base64 编码的数组；
一般这种数据后面很有可能用得到，变量名改成 qs_base_data 然后全局批量替换这个变量

结果：

var encode_version = "sojson.v5",
    brfui = "",
    qs_base_data /* _0x2c67 */ = ["VHtkTcOA", "DcOPVEB/", "UMK...o9WPA=="];
然后浏览器打开一个空白页面，手动复制这段代码到控制台进去，执行，为我们接下来的调试做准备。
或者创建一个空白页面，把这行代码放进去准备执行。

第二段 (一个匿名函数)
(function (_0x2ac5c, _0x3068dd) {
    var _0xe9c838 = function (_0x23ef01) {
        while (--_0x23ef01) {
            _0x2ac5c["push"](_0x2ac5c["shift"]());
        }
    };
    var _0x3421c5 = function () {
        var _0x5d77e7 = {
            data: {
                key: "cookie",
                value: "timeout"
            },
            setCookie: function (_0x3c4b91, _0x3bd290, _0x5a5c5a, _0x529726) {
                _0x529726 = _0x529726 || {};
                var _0xe0bed = _0x3bd290 + "=" + _0x5a5c5a;
                var _0x45867b = 0;
                for (var _0x45867b = 0, _0x568496 = _0x3c4b91["length"]; _0x45867b < _0x568496; _0x45867b++) {
                    var _0x24edef = _0x3c4b91[_0x45867b];
                    _0xe0bed += "; " + _0x24edef;
                    var _0x35ae72 = _0x3c4b91[_0x24edef];
                    _0x3c4b91["push"](_0x35ae72);
                    _0x568496 = _0x3c4b91["length"];
                    if (_0x35ae72 !== !![]) {
                        _0xe0bed += "=" + _0x35ae72;
                    }
                }
                _0x529726["cookie"] = _0xe0bed;
            },
            removeCookie: function () {
                return "dev";
            },
            getCookie: function (_0x475fb9, _0x248e39) {
                _0x475fb9 = _0x475fb9 || function (_0x34f7a4) {
                    return _0x34f7a4;
                };
                var _0x202cdc = _0x475fb9(new RegExp("(?:^|; )" + _0x248e39["replace"](/([.$?*|{}()[]\/+^])/g, "$1") + "=([^;]*)"));
                var _0x48a974 = function (_0x267c1a, _0x45329b) {
                    _0x267c1a(++_0x45329b);
                };
                _0x48a974(_0xe9c838, _0x3068dd);
                return _0x202cdc ? decodeURIComponent(_0x202cdc[1]) : undefined;
            }
        };
        var _0x1dbeed = function () {
            var _0x4885e3 = new RegExp("\\w+ *\\(\\) *{\\w+ *['|\"].+['|\"];? *}");
            return _0x4885e3["test"](_0x5d77e7["removeCookie"]["toString"]());
        };
        _0x5d77e7["updateCookie"] = _0x1dbeed;
        var _0x1cefc6 = "";
        var _0x170183 = _0x5d77e7["updateCookie"]();
        if (!_0x170183) {
            _0x5d77e7["setCookie"](["*"], "counter", 1);
        } else if (_0x170183) {
            _0x1cefc6 = _0x5d77e7["getCookie"](null, "counter");
        } else {
            _0x5d77e7["removeCookie"]();
        }
    };
    _0x3421c5();
})(qs_base_data, 202);
现看这个匿名函数的入口点，传入了两个参数，qs_base_data, 202
所以参数一的名字也可以替换了，参数二格式化前其实是一个 16 进制 0xca，这里自动转换为 10 进制了。
我们暂且把参数二称为 qs_arg_num，再次全局替换。

然后看方法里面第一段，定义了一个方法：

    var _0xe9c838 = function (_0x23ef01) {
        while (--_0x23ef01) {
            qs_base_data["push"](qs_base_data["shift"]());
        }
    };
然后我们会看到 qs_base_data["push"](qs_base_data["shift"]()); 这一段，
我们记得 qs_base_data 是一个数组，["push"] 其实就是调用这个数组的方法。
我们先全局处理一遍，把 qs_base_data["push"] 替换为 qs_base_data.push 这样更方便我们查看。
编辑器内用正则表达式替换 ([a-zA-z0-9_]+)\[['"]([^"']+)['"]\] 替换为 $1.$2，记得勾选 .* 表示正则替换。
替换完以后如果还能找到就继续替换，一直到找不到匹配项为止。

虽然我们不知道这段代码是什么用途(当然，通过阅读代码，大致可以理解是把数组后面的一定数量移动到数组前面)，
我们可以找哪里调用了这个方法 _0xe9c838，好知道参数一是从哪里来的。

我们会看到，全局只有一个地方用到了这个方法(名)，_0x48a974(_0xe9c838, qs_arg_num);
如果有多个地方用的，那么就要挨个去测试哪里真正调用的。

继续找 _0x48a974 发现前一行定义了这个方法，内容是将参数二自增后传递给参数一。

var _0x48a974 = function (_0x267c1a, _0x45329b) {
    _0x267c1a(++_0x45329b);
};
我们就知道参数一是最开始入口点的 qs_arg_num，给对应的方法起名。
最终相关代码应该是这样的。

var qs_arr_move /* _0xe9c838 */ = function (qs_arg_num /* _0x23ef01 */) {
    while (--qs_arg_num) {
        qs_base_data.push(qs_base_data.shift());
    }
};
var call_fun /* _0x48a974 */ = function (callback_fun /* _0x267c1a */, qs_arg_num /* _0x45329b */) {
    callback_fun(++qs_arg_num);
};
call_fun(qs_arr_move, qs_arg_num);
到了这一步，我现在接下来的动作有两个不同的方向：

继续寻找这个方法是从哪里调用的
回到最开始，按解释器角度继续阅读代码
我这里选择按解释器来执行代码，我们回到最开始的 qs_arr_move 方法后面。
后面又定义了一个 _0x3421c5 的方法，并且紧接着执行了这个方法。
所以，我们可以看下这个方法内部做了什么，由于没有参数，直接进入方法第一行往下看。
在方法内，又定义了一个 _0x5d77e7 的对象，并且定义了 data、 setCookie、 removeCookie、 getCookie 几个方法，
我们先给这个对象(_0x5d77e7)起名，就叫他 qs_obj_cookie 吧。
由于并没有执行，我们先不管，继续往下...

var _0x1dbeed = function () {
    var _0x4885e3 = new RegExp("\\w+ *\\(\\) *{\\w+ *['|\"].+['|\"];? *}");
    return _0x4885e3.test(qs_obj_cookie.removeCookie.toString());
};
qs_obj_cookie.updateCookie = _0x1dbeed;
var _0x170183 = qs_obj_cookie.updateCookie();
后面定义了 _0x1dbeed 这个方法，并赋值给了 qs_obj_cookie.updateCookie，所以我们叫他 qs_updateCookie 吧。
然后只定义了一个空变量后，就执行了这个方法并保存返回值到 _0x170183。
我们回过头阅读下这个 updateCookie 里面到底做了啥。
定义一个正则表达式，并把 removeCookie 方法转换为字符串进行匹配。
代码拿到浏览器控制台执行，

var removeCookie = function () {
    return "dev";
}
var _0x1dbeed = function () {
    var _0x4885e3 = new RegExp("\\w+ *\\(\\) *{\\w+ *['|\"].+['|\"];? *}");
    return _0x4885e3.test(removeCookie.toString());
}
_0x1dbeed();
我们看到最终执行的是，并返回了 false

/\w+ *\(\) *{\w+ *['|"].+['|"];? *}/.test('function () {
    return "dev";
}');
但是，这里有一个坑，如果熟悉正则表达式的朋友应该会发现，这个正则表达式不匹配换行，
而我们 toString 得到的方法字符串是带有换行的，因为我们格式化过了，
如果不熟的话，也应该有一个警觉，removeCookie 方法本身没有多大实际意义，
然后这里用正则表达式去匹配一个方法是什么目的？
其实，这个代码就是用来防格式化的，因为格式化以后就会返回 false，我们拿未格式化的代码进去就会返回 true。
先不管，我们知道这里应该为 true 就好，继续往下执行，

下面是一个 if 判断，用来判断刚才返回的是 true 还是 false，我们实际是 true，直接进入对应条件。

_0x1cefc6 = qs_obj_cookie.getCookie(null, "counter");

回到 getCookie 的定义，我们看下都干了啥。

getCookie: function (_0x475fb9, _0x248e39) {
    _0x475fb9 = _0x475fb9 || function (_0x34f7a4) {
        return _0x34f7a4;
    };
    var _0x202cdc = _0x475fb9(new RegExp("(?:^|; )" + _0x248e39.replace(/([.$?*|{}()[]\/+^])/g, "$1") + "=([^;]*)"));
    var call_fun /* _0x48a974 */ = function (callback_fun /* _0x267c1a */, qs_arg_num /* _0x45329b */) {
        callback_fun(++qs_arg_num);
    };
    call_fun(qs_arr_move, qs_arg_num);
    return _0x202cdc ? decodeURIComponent(_0x202cdc[1]) : undefined;
}
先给参数一赋值一个方法，方法直接把参数返回...
所以，参数一的名字有了，qs_fun_back 真的很随意的起了名字...
参数二是一个字符串，就先起名叫 qs_name_str 吧...

紧接着往下面看，定义了一个 _0x202cdc 变量并赋值了一个正则表达式然后在最末尾返回了，
而最外面调用 getCookie 的地方虽然保存到了 _0x1cefc6，但并没有任何地方使用，暂时先忽略这里的代码，继续往下看。

这里就会看到我们最开始调用 qs_arr_move 的地方了，然后整个大的方法就执行完了。

总结一下这个方法做了什么：

尝试判断代码是否被格式化
如果没有格式化，就去移动数组的元素。
我们直接在浏览器控制台执行最终有意义的代码：

var qs_arr_move /* _0xe9c838 */ = function (qs_arg_num /* _0x23ef01 */) {
    while (--qs_arg_num) {
        qs_base_data.push(qs_base_data.shift());
    }
};
qs_arr_move(qs_base_data, 203 /* ++qs_arg_num */);
实际理解代码，就是把数组从某个位置截断，前面的附加在后面。

arr = [].concat(arr.splice(202 % arr.length, arr.length - (202 % arr.length)) arr);
第三段 (又一个匿名函数)
由于上面已经讲了一些调试的方法，所以这里可能会跳过一些和上面相似但可能重要的流程。

继续看，先定义了一个方法，叫_0xdf0e ，然后全局搜索这个方法名，发现有很多处调用，这里先找到第一个调用作为理解。
是在一个定时器里面循环调用的，先不管定时器做了啥，我们先拿着参数回到方法看方法要干啥。

setInterval(function () {
    var _0x346bb8 = {
        gHwtC: function _0x4f689e(_0x2f31c2) {
            return _0x2f31c2();
        }
    };
    _0x346bb8[_0xdf0e("0x0", "*naR")](_0x10c488);
}, 4e3);
在方法体内寻找这两个参数在什么地方使用，相关关键代码如下：

_0x532a72 = _0x532a72 - 0;
var _0x44d30c = qs_base_data[_0x532a72];

_0x44d30c = _0xdf0e.rc4(_0x44d30c, _0x422b57);
方法最开始对参数一 _0x532a72(0x0) -0 操作，实际就是将 16 进制的字符串转为十进制数值。
然后又从 qs_base_data 取出一个成员，最后放进 rc4 里面，根据理解，应该是取出密文用密钥解密。
那么，参数分别就起名 qs_arg_id，qs_arg_str，相关变量为 qs_data_item。
为了保险起见，我们可以继续看下后面的代码都做了啥(其实上面的代码就是所有需要的了，其他的都是多余的“花指令”)

首先在对象未初始化时，获取 /创建 window，atob(base64 decode)
然后创建 _0x481c88 方法赋值给 _0xdf0e.rc4 = _0x481c88;
就是所谓的 rc4 解密了。
初始化后，再次获取了 var qs_data_item2 /* _0x5597dd */ = _0xdf0e.data[qs_arg_id];

然后，关键代码有这样一段（已经整理后的代码）

var qs_data_item2 /* _0x5597dd */ = _0xdf0e.data[qs_arg_id];
if (qs_data_item2 === undefined) {
    if (_0xdf0e.once === undefined) {
        var qs_rc4_state /* _0xccf703 */ = function (rc4Bytes /* _0x499f89 */) {
            this.rc4Bytes = rc4Bytes;
            this.states = [1, 0, 0];
            this.newState = function () {
                return "newState";
            };
            this.firstState = "\\w+ *\\(\\) *{\\w+ *";
            this.secondState = "['|\"].+['|\"];? *}";
        };
        //...
        new qs_rc4_state(_0xdf0e).checkState();
        _0xdf0e.once = !![];
    }
    qs_data_item = _0xdf0e.rc4(qs_data_item, qs_arg_str);
    _0xdf0e.data[qs_arg_id] = qs_data_item;
} else {
    qs_data_item = qs_data_item2;
}
他把至今没有定义名字的 _0xdf0e 传递给 qs_rc4_state 并保存在了 rc4Bytes，
所以我们也可以把这个变量 _0xdf0e 定义为 rc4Bytes 了。

继续跟代码，和上面 cookies 那一段几乎一样目的的代码，还是判断代码是否格式化。
如果弄不清楚代码执行结果，就把代码放到浏览器执行一遍就可以了。

var reg_ex = new RegExp("\\w+ *\\(\\) *{\\w+ *" + "['|\"].+['|\"];? *}");
var newState = function(){return'newState';}; // 这里不要格式化，因为就是测试她是否格式化的
var states = [1, 0, 0];
reg_ex.test(newState.toString()) ? --states[1] : --states[0];
这个代码最后会输出 -1，然后进入 runState 方法

qs_rc4_state.prototype.runState = function (_0x24db9c) {
    if (!Boolean(~_0x24db9c)) {
        return _0x24db9c;
    }
    return this.getState(this.rc4Bytes);
};
!Boolean(~-1) 返回 true 所以这里就返回了，没有后续。
而如果代码被格式化了，后续的代码则是进入死循环...

跳出这个方法，我们看后续又做了什么，

var qs_data_item2 /* _0x5597dd */ = qs_rc4Bytes.data[qs_arg_id];
if (qs_data_item2 === undefined) {
    if (qs_rc4Bytes.once === undefined) {
        // 防止代码格式化的代码
    }
    qs_data_item = qs_rc4Bytes.rc4(qs_data_item, qs_arg_str);
    qs_rc4Bytes.data[qs_arg_id] = qs_data_item;
} else {
    qs_data_item = qs_data_item2;
}
return qs_data_item;
最终执行的意思即是：

从 qs_base_data 取出密文并根据调用时的密钥使用 rc4 解密，
然后保存到自身的解密结果数组中
下次这直接使用解密后的结果
返回解密的结果
PS：其实这也是他的代码中最核心的一部分代码了，其他的都是围绕这个功能打掩护而已。

我们将最终整理出来的核心代码放入浏览器执行，用以后续代码的理解。

var qs_base_data = ["VHtkTcOA", "DcOPVEB/", "UMKdX8KJ", "SsOnD3DDtQ==", "wolyw6h7QA==", "DlVDwp0=", "w6LCvBIsw6U=", "a8OcBkHDng==", "V8OeWyjDvQ==", "woAMIcKxMg==", "MBVaGsO5", "wr98woZiw6s=", "bho7", "NMOJdivCu1nDmCHCg8KBw4HDqy0=", "YsOKwrvCpk3DqMOgwq1N", "wpVhDsKkw6ws", "JErDp8KOJcKLw7Fh", "DMO/w6dSE0DDmk3CihTCmMKtw7g=", "6L+15pum5LuZ5LuI5Lqg57CV5Ym4B8Oc5pKR5L2h44Cs", "56qx6ZS45o6U6aqB57iwFOKBpmDDo+WLkOWsouKCicOX5ZCYw6nigKIpYeikhOWskOKDvMK7776t5L2T5Y6m5L+t55u3XsOlw67jgbE=", "5aaB5pyP5oKq55uuw6c36Yel5bev5aeL5LugdMK3WO+8pnY1DOahqeesne+9p+ethuetvuWGqeS6pOmciGQ1wrTDoBDCgMKiXMO/I+eYveS7r+egtO+/vOiup+aPl+WNsuWGpeafpeWHleWIreWvleODhOi8n+S7vOW3heWGu+S5tOiCheWIvuWunnDCsQ3jgrhzKMOX56+45quw54uK5Yah5ayU", "U8KCCSTCtw==", "SEB6ccOl", "dsOwwqrCi0c=", "ZcOXwq/ClhA=", "bBtxw4DDjw==", "KcOhwr5Qwps=", "wolww5BvcA==", "w7/Ds0TDhcOd", "Z8Kmw60Rw6Q=", "SsOvwrnCqiQ=", "E8OAdnZ6", "IVDDp8KePsKOw7s=", "wp3DnEzCoS3CrBk=", "JsOYw6vCvxzDvcK4wrQbw6Ekw6JrwpbDhMKdMQ==", "w6AmGMKMwqU=", "JMOGcEZt", "wrfCgHlh", "woHDv8OkQcKFJzcpw70=", "V19x", "S8OnwpFZw4M=", "woUbKcKqJQ==", "acKTw5oH", "w7N/eHbCrQ==", "wqJgXQvCtw==", "wpfDt8OrTcKB", "w6Eqwp8AVU5H", "WMK7wrjCgcOF", "dMOLwrHCsETDrcOr", "w65jf3o=", "O8OQw5LChUvCtcOo", "wonDklDCvA==", "fcKdw4Yaw5rDtcK2", "w4TCmcOp", "TcO6wo1Fw57CmsOS", "YMONJzLDu1HCgjLDmQ==", "w7EEw4fDmMOh", "OMOuwrhhKitT", "M8OTflp+", "wplxw5lCUg==", "w5c9w5TDhcOp", "dsOTHiDDuA==", "wqloEcKKw6k=", "RcONM8Oow5U=", "S8KBw5gfw7E=", "wqYyH8KyMQ==", "w6vCoycLw78=", "McOgTX5f", "Ul5wdw==", "wqlNw7ZsVg==", "TcKtwrDCjMOGwoE=", "w7DCgcKpRRU=", "wo1iFsKvw6Y2Cg==", "w6HDi0LDqcOY", "wppyw6HDjznDqsKswooL", "w7nCvD0Uw6nCnQEVLw==", "dsOIwrrCsV8=", "5Lma6IGL5Yi86ZmLasKzcGATw7wbwpcp", "K8O6wq1Uwo8t", "wqFjw5RRZ8ON", "a8Otwptiw6A=", "EFdWwo/CtA==", "AAVow4vDiQ==", "T8O2wqZcw4U=", "w51lQVbCvg==", "worDn8O2bMKx", "E8OYwpB+Ew==", "esO7wq16dA==", "c8OEOXzDjw==", "WcKrw5stw5Q=", "ecOVKMOgwoY=", "w7MmAcKCwrQ=", "wrLDo04Nw4M1w7XDt8KG", "CUBcwoDCkA==", "wqBpw5kYZsOBQMK0w5nCoHnCjsOHZw==", "w6luN3LCpsKcwqBBw7E4w4Y=", "e8OpwqPCkxvCiQ==", "cMOjbgPDmQ==", "w7LDuggUwoU6wqM=", "acO8awQ=", "Bi5hw5bDnEw=", "GiZfPcO4WQ==", "wpbCkMOOw63CtcOSKMKzw4TDssOSw7kmDxop", "V3ViwqbCqMKEXzXCrsOXwqbDuMOvQ3VDUn/DtjXDnmN2H8Kawo0Ew5liEMOww63CqcOfL8K6CMObwqzCkcO5w53CiMKoAhzCh8KldD5QYVbDt3pVVQLCuGE0ew==", "R8O7wopC", "YMO7YwPDgw==", "w6PCvScSw7I=", "woAOO8KzOw==", "w5sNOMKVwp8=", "cyY4wqI4", "NMOlwo9WPA=="];
var qs_arr_move = function (qs_arg_num) {
    while (--qs_arg_num) {
        qs_base_data.push(qs_base_data.shift());
    }
};
qs_arr_move(203);
var qs_rc4Bytes = function (qs_arg_id, qs_arg_str) {
    qs_arg_id = qs_arg_id - 0;
    var qs_data_item  = qs_base_data[qs_arg_id];
    if (qs_rc4Bytes.initialized === undefined) {
        qs_rc4Bytes.rc4 = function (_0x8f6596, _0x40168c) {
            var _0x155004 = [], _0x132490 = 0, _0x55539c, _0x432c06 = "", _0x4c5e8c = "";
            _0x8f6596 = atob(_0x8f6596);
            for (var _0x330500 = 0, _0x16d6ad = _0x8f6596.length; _0x330500 < _0x16d6ad; _0x330500++) {
                _0x4c5e8c += "%" + ("00" + _0x8f6596.charCodeAt(_0x330500)["toString"](16))["slice"](-2);
            }
            _0x8f6596 = decodeURIComponent(_0x4c5e8c);
            for (var _0x163351 = 0; _0x163351 < 256; _0x163351++) {
                _0x155004[_0x163351] = _0x163351;
            }
            for (_0x163351 = 0; _0x163351 < 256; _0x163351++) {
                _0x132490 = (_0x132490 + _0x155004[_0x163351] + _0x40168c.charCodeAt(_0x163351 % _0x40168c.length)) % 256;
                _0x55539c = _0x155004[_0x163351];
                _0x155004[_0x163351] = _0x155004[_0x132490];
                _0x155004[_0x132490] = _0x55539c;
            }
            _0x163351 = 0;
            _0x132490 = 0;
            for (var _0x4ebeb6 = 0; _0x4ebeb6 < _0x8f6596.length; _0x4ebeb6++) {
                _0x163351 = (_0x163351 + 1) % 256;
                _0x132490 = (_0x132490 + _0x155004[_0x163351]) % 256;
                _0x55539c = _0x155004[_0x163351];
                _0x155004[_0x163351] = _0x155004[_0x132490];
                _0x155004[_0x132490] = _0x55539c;
                _0x432c06 += String.fromCharCode(_0x8f6596.charCodeAt(_0x4ebeb6) ^ _0x155004[(_0x155004[_0x163351] + _0x155004[_0x132490]) % 256]);
            }
            return _0x432c06;
        };
        qs_rc4Bytes.data = {};
        qs_rc4Bytes.initialized = true;
    }
    var qs_data_item2  = qs_rc4Bytes.data[qs_arg_id];
    if (qs_data_item2 === undefined) {
        qs_data_item = qs_rc4Bytes.rc4(qs_data_item, qs_arg_str);
        qs_rc4Bytes.data[qs_arg_id] = qs_data_item;
    } else {
        qs_data_item = qs_data_item2;
    }
    return qs_data_item;
};
然后调用第一个 qs_rc4Bytes("0x0", "*naR") 进行测试，得到 gHwtC
定时器的目的大概是定时调用一个方法，对应方法的定义在下面...

由于后面的代码很多都是被加密的，所以写个 JS 脚本批量替换后面加密的数据为原文

# 这里的 js_str 就是 160 行 之后的所有代码(字符串)
var js_str = $('#compressor-textarea').val(), // 此文代码格式页面的文本框
    reg_ex = /qs_rc4Bytes\(['"](0x[0-9a-f]+)['"],\s*['"]([^"']+)['"]\)/g;
js_str = js_str.replace(reg_ex, function(match_str, str, key){
    return '"' + qs_rc4Bytes(str, key) + '"';
})
// 再次去除数组调用的对象方法
.replace(/([a-zA-z0-9_]+)\[['"]([^"']+)['"]\]/g, function(match_str, str, key) {
    return str + '.' + key;
});
$('#compressor-textarea').val(js_str);
替换后的代码存于 jiemi.js

今天不早了，就先解析到这里，后续的代码，改天抽时间继续分析。

赶紧洗干净了去玩游戏去~~
