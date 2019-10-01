# Cookie

您可能会注意到，商店和CAT组件上下文具有属性cookie。实际上，它是一个通用包装器，可以以与环境无关的方式`get`或`set`cookie。

它具有以下界面：

```js
/**
 * Gets a map of cookie values by their names.
 * @returns {Object} The cookies map by their names.
 */
getAll();

/**
 * Gets a cookie value by its name.
 * @param {string} name The cookie name.
 * @returns {string} The cookie value.
 */
get(name);

/**
 * Gets current cookie string.
 * @returns {string} Cookie string.
 */
getCookieString();

/**
 * Sets cookie to this wrapper.
 * @param {Object} cookieSetup Cookie setup object.
 * @param {string} cookieSetup.key Cookie key.
 * @param {string} cookieSetup.value Cookie value.
 * @param {number?} cookieSetup.maxAge Max cookie age in seconds.
 * @param {Date?} cookieSetup.expires Expire date.
 * @param {string?} cookieSetup.path URI path for cookie.
 * @param {string?} cookieSetup.domain Cookie domain.
 * @param {boolean?} cookieSetup.secure Is cookie secured.
 * @param {boolean?} cookieSetup.httpOnly Is cookie HTTP only.
 * @returns {string} Cookie setup string.
 */
set(cookieSetup);
```

