## Lifecycle
1. **Session Creation:** deals with user's providing their credentials.
2. **Session Tracking:** deals with user's actions performed after authentication.
3. **Session Expiry:** deals with user's that forget to log out of the application.
4. **Session Termination:** deals with user's pressing the logout button.

## Cookies vs Tokens

| **Cookie-Session Management**                                                                                                                         | **Token-Based Session Management**                                                                                                                                        |
| ----------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cookie is automatically sent by the browser with each request                                                                                         | Token has to be submitted as a header with each request using client-side JavaScript                                                                                      |
| Cookie attributes can be used to enhance the browser's protection of the cookie                                                                       | Tokens do not have automatic security protections enforced and should, therefore, be safeguarded against disclosures                                                      |
| Cookies can be vulnerable to conventional client-side attacks such as CSRF, where the browser is tricked into making a request on behalf of the user. | As the token is not automatically added to any request and cannot be read from LocalStorage by other domains, conventional client-side attacks such as CSRF are blocked.  |
| As cookies are locked to a specific domain, it can be difficult to use them securely in decentralised web applications.                               | Tokens work well in decentralised web applications, as they are managed through JavaScript and can often contain all the information required to verify the token itself. |

## Example in Lifecycle mapping

Source: https://tryhackme.com/room/sessionmanagement

| **Lifecycle Phase** | **Observation**                                                                                  |
| ------------------- | ------------------------------------------------------------------------------------------------ |
| Session Creation    | A combination of cookie- and token-based session management is being used                        |
| Session Creation    | A new session value is provided for each login attempt                                           |
| Session Creation    | The session value itself appears to be sufficiently random                                       |
| Session Tracking    | Unauthenticated actions are not tracked via a session                                            |
| Session Tracking    | The cookie is sent in each request for tracking                                                  |
| Session Tracking    | Several requests can be made without a cookie                                                    |
| Session Tracking    | The initial token values that are loaded post-authentication dictate the visible information     |
| Session Expiry      | There is a mismatch between the client-side and server-side expiry times                         |
| Session Expiry      | The expiry time is incredibly long                                                               |
| Session Termination | Users can forcibly terminate their session                                                       |
| Session Termination | Sessions are terminated server-side but reusing an old session leads to an internal server error |

Given all of this information, we would already report the following vulnerabilities on a security assessment:
- Excessive Session Lifetimes

We would also need to investigate the following further:
- Access controls on all API endpoints
- Web application logs for the accountability of actions