---
layout: post
category : lessons
tags : [shiro, gwt, rpc, ajax]
---
{% include JB/setup %}


##GWT.rpc and shiro redirects

By default Apache Shiros UserFilter save request and redirects to login page. This will not work well whith rpc/ajax requests.

Its easy to override UserFilter.onAccessDenied and return 401 if it is a rpc request. See example below.

```java	
	/**
	 * This default implementation simply calls
	 * {@link #saveRequestAndRedirectToLogin(javax.servlet.ServletRequest, javax.servlet.ServletResponse) 
	 * saveRequestAndRedirectToLogin}
	 * and then immediately returns <code>false</code>, thereby preventing the chain from continuing so 
	 * the redirect may execute.
	 */
	@Override
	protected boolean onAccessDenied(ServletRequest request, ServletResponse response) throws Exception {
	
		//If it is gwt-rpc don't redirect instead return 401
		if (((HttpServletRequest) request).getRequestURL().toString().contains("GWT.rpc") 
			&& request.getContentType().contains("x-gwt-rpc")) {
			((HttpServletResponse) response).sendError(HttpServletResponse.SC_UNAUTHORIZED);
			return false;
		} else {
			saveRequestAndRedirectToLogin(request, response);
			return false;
		}
	}
```
	
