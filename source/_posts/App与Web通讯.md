---
title: App与Web通讯
date: 2016-07-30 15:22:45
toc: true
categories: Android
tags:
- Android
- HTML5
---

对于大多数App来说，并非100%的纯Native开发模式。有时候为了运营方便，会提供一个内置浏览器『Activity包含一个WebView』来加载后端的Web页面，从而可以达到App不发版，通过改动远程的Web页面来达到动态发布的目的。那么问题来了，如果后端的Web页面需要跟App的Native通讯，如何实现呢？下面介绍几种通讯方式。

### 方式

- App通过WebView加载网页时，将参数拼在url后面
- App与Web通过JavaScript桥
- Cookie

### 实现

#### URL拼接

举例：

~~~
APP中打开http://foo.bar/hello.html页面, 但是页面需要知道当前登录用户的uid
~~~

Android代码

~~~ Android
private static final String URL = "http://foo.bar/hello.html";

// get uid
String uid = getUid();
mWebView.loadUrl(String.fromat("%s?uid=%s", URL, uid));
......
~~~

#### JavaScript桥

举例：

~~~
点击H5页面中的分享按钮，调用App的分享通道。并把分享成功与否告诉H5
~~~

H5代码

~~~ HTML
......
<body>
<p id="share">Click this button to share :</p>
<button onclick="callShare()">Share</button>
</body>
......
<script>
// 调用Native分享接口
function callShare() {
  /**
  * callShare 四个参数
  * param title 分享的标题
  * param content 分享的内容『用于分享给微信好友时，气泡窗中缩略图右侧的文案』
  * param thumb_url 分享的缩略图 分享出去时，显示的缩略图
  * param url 分享出去的url， 供传播的用户点击进入h5页面
  */
  window.Demo.callShare("This is title", "This is Content", "http://shareThumbUrl", "http://shareUrl");
}

// Native分享接口分享成功后的回调
function onShareSuccess() {
	alert("share success!");
}
// Native分享接口分享成功后的回调
function onShareFailed() {
	alert("share failed!");
}
</script>
......
~~~

Android代码

~~~ Android
......
// 添加JavaScript桥
mWebView.addJavascriptInterface(new JSBridge(this, mWebView), "Demo");
......
// JSBridge类
class JSBridge {
    ......
    /**
     * 分享
     *
     * @param title
     * @param content
     * @param thumb_url
     * @param url
     */
    @JavascriptInterface
    @JavascriptInterface
    public void horizonShare(final String title, final String content, final String thumb_url, final String url) {
        //do share
        ShareUtils.share(ctx, title, content, thumbUrl, url,
                new PlatformActionListener() {
                    @Override
                    public void onComplete(Platform platform, int i, HashMap<String, Object> hashMap) {
                        mWebView.post(new Runnable() {
                            @Override
                            public void run() {
                                mWebView.loadUrl("javascript:onShareSuccess()");
                            }
                        });
                    }

                    @Override
                    public void onError(Platform platform, int i, Throwable throwable) {
                        if (BuildConfig.DEBUG) {
                            throwable.printStackTrace();
                        }
                        mWebView.post(new Runnable() {
                            @Override
                            public void run() {
                                mWebView.loadUrl("javascript:onShareFailed()");
                            }
                        });
                    }

                    @Override
                    public void onCancel(Platform platform, int i) {
                        mWebView.post(new Runnable() {
                            @Override
                            public void run() {
                                mWebView.loadUrl("javascript:onShareFailed()");
                            }
                        });
                    }
                });
    }
    ......
}
~~~

#### Cookie

例如：

~~~
APP中通过WebView打开的页面需要获取当前登录用户的登录信息；同时，如果APP
处于未登录状态，但是当在WebView的页面中登录了，需要将登录状态同步到APP。
~~~

Android代码

- Webview所在Activity

~~~ Android
......
@Override
protected void onCreate(Bundle savedInstanceState) {
    ......
    CookieHelper.syncAppInfoToH5(this);
}

@Override
protected void onDestroy() {
    CookieHelper.syncH5InfoToApp(this);
    ......
}
~~~

- CookieHelper

~~~ Android
/**
 * Cookie 管理辅助工具
 *
 * Created by wangsheng on 16/7/28.
 */

public class CookieHelper {
    private static final String COOKIE_DOMAIN = ".foo.bar";
    private static final String KEY_IS_LOGIN = "islogin";
    private static final String KEY_USERNAME = "username";
    private static final String KEY_UID = "uid";

    /**
     * 将App的登录信息同步到h5的cookie中
     *
     * @param ctx Context
     */
    public static void syncAppInfoToH5(Context ctx) {
        if (isLoginStatus(ctx)) {
            UserInfo mUserInfo = getUserInfo(ctx);
            if (mUserInfo != null) {
                if (Build.VERSION.SDK_INT < Build.VERSION_CODES.LOLLIPOP) {
                    CookieSyncManager.createInstance(ctx);
                }
                CookieManager cookieManager = CookieManager.getInstance();
                cookieManager.setAcceptCookie(true);
                cookieManager.setCookie(COOKIE_DOMAIN, String.format("%s=%s", KEY_IS_LOGIN, 1));
                cookieManager.setCookie(COOKIE_DOMAIN,
                        String.format("%s=%s", KEY_USERNAME, mUserInfo.username));
                cookieManager.setCookie(COOKIE_DOMAIN,
                        String.format("%s=%s", KEY_UID, mUserInfo.uid));
                if (Build.VERSION.SDK_INT < Build.VERSION_CODES.LOLLIPOP) {
                    CookieSyncManager.getInstance().sync();
                }
            }
        }
    }

    /**
     * 将h5的cookie中的登录信息同步到app中
     *
     * @param ctx Context
     */
    public static void syncH5InfoToApp(Context ctx) {
        if (!isLoginStatus(ctx)) {
            CookieManager cookieManager = CookieManager.getInstance();
            String cookies = cookieManager.getCookie(COOKIE_DOMAIN);
            if (!TextUtils.isEmpty(cookies)) {
                String isLogin = null;
                String username = null;
                String uid = null;
                String[] cookieArray = cookies.split(";");
                for (String cookie : cookieArray) {
                    String[] kvs = cookie.trim().split("=");
                    if (kvs.length == 2) {
                        switch (kvs[0]) {
                            case KEY_IS_LOGIN:
                                isLogin = kvs[1];
                                break;
                            case KEY_USERNAME:
                                username = kvs[1];
                                break;
                            case KEY_UID:
                                uid = kvs[1];
                                break;
                        }
                    }
                }

                if (!TextUtils.isEmpty(isLogin) && isLogin.equals("1") && !TextUtils.isEmpty(
                        username) && !TextUtils.isEmpty(uid)) {
                    saveLoginStatus(ctx, true);
                    UserInfo mUserInfo = getUserInfo(ctx);
                    if (mUserInfo == null) {
                        mUserInfo = new UserInfo(uid, username);
                    } else {
                        mUserInfo.uid = uid;
                        mUserInfo.username = username;
                    }
                    saveUserInfo(ctx, mUserInfo);
                }
            }
        }
    }
}
~~~
