--
layout: post
category : lessons
tags : [shiro, gwt, rpc, ajax]
---
{% include JB/setup %}


##GWT.rpc and shiro redirects

By default Apache Shiro's UserFilter save request and redirects to login page. This will not work well whith rpc/acjax requests.

It's easy to override onAccessDenied and return 401 if it is a rpc request. See example below.

	public class RPCEnabledUserFilter extends UserFilter {

		/**
		 * Returns <code>true</code> if the request is a
		 * {@link #isLoginRequest(javax.servlet.ServletRequest, javax.servlet.ServletResponse) loginRequest} or
		 * if the current {@link #getSubject(javax.servlet.ServletRequest, javax.servlet.ServletResponse) subject}
		 * is not <code>null</code>, <code>false</code> otherwise.
		 *
		 * @return <code>true</code> if the request is a
		 *         {@link #isLoginRequest(javax.servlet.ServletRequest, javax.servlet.ServletResponse) loginRequest} or
		 *         if the current {@link #getSubject(javax.servlet.ServletRequest, javax.servlet.ServletResponse) subject}
		 *         is not <code>null</code>, <code>false</code> otherwise.
		 */
		@Override
		protected boolean isAccessAllowed(ServletRequest request, ServletResponse response, Object mappedValue) {
			if (isLoginRequest(request, response)) {
				return true;
			} else {
				Subject subject = getSubject(request, response);
				// If principal is not null, then the user is known and should be allowed access.
				return subject.getPrincipal() != null;
			}
		}
	
		/**
		 * This default implementation simply calls
		 * {@link #saveRequestAndRedirectToLogin(javax.servlet.ServletRequest, javax.servlet.ServletResponse) saveRequestAndRedirectToLogin}
		 * and then immediately returns <code>false</code>, thereby preventing the chain from continuing so the redirect may
		 * execute.
		 */
		@Override
		protected boolean onAccessDenied(ServletRequest request, ServletResponse response) throws Exception {
	
			//If it is gwt-rpc don't redirect instead return 401
			if (((HttpServletRequest) request).getRequestURL().toString().contains("GWT.rpc") && request.getContentType().contains("x-gwt-rpc")) {
				((HttpServletResponse) response).sendError(HttpServletResponse.SC_UNAUTHORIZED);
				return false;
			} else {
				saveRequestAndRedirectToLogin(request, response);
				return false;
			}
		}

	}
	
