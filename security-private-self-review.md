## Responses to the [Self-Review Questionnaire: Security and Privacy](https://w3ctag.github.io/security-questionnaire/) for the [WebRequest SecurityInfo API]

1. **What information might this feature expose to Web sites or other parties, and for what purposes is that exposure necessary?**

    When a `ControlledFrame` intercepts a request, this feature exposes the server's TLS certificate information (including connection state, leaf certificate, fingerprint, and optionally raw DER bytes) to the Isolated Web App.

    This exposure is necessary to allow the application to verify the trustworthiness of a server. The app can use the fingerprint obtained from this browser-verified connection to validate a separate raw TCP/UDP connection (established via Direct Sockets) to the same server, thereby preventing man-in-the-middle (MITM) attacks on custom protocols.

2. **Do features in your specification expose the minimum amount of information necessary to enable their intended uses?**

    Yes. Access to this information is strictly opt-in via `WebRequestInterceptorOptions` to minimize performance overhead and unnecessary data exposure.
    
    * `securityInfo: true` must be set to receive basic details (state, fingerprint).
    * `securityInfoRawDer: true` must be explicitly set to receive the raw certificate bytes.
    * Only the leaf certificate is exposed.

3. **How do the features in your specification deal with personal information, personally-identifiable information (PII), or information derived from them?**

    This specification deals with server identity information.

    It exposes the public TLS certificate of the server, which is generic public information provided during a standard handshake. It does not process or expose user PII.

4. **How do the features in your specification deal with sensitive information?**

    Same as in #3. The specification handles cryptographic server identities.

5. **Do the features in your specification introduce new state for an origin that persists across browsing sessions?**

    No. The `SecurityInfo` object is transient and attached to specific network request events (`onHeadersReceived`).

6. **Do the features in your specification expose information about the underlying platform to origins?**

    No. It exposes the verification state derived from the network stack (e.g., whether the certificate is trusted), but not details of the underlying OS.

7. **Does this specification allow an origin to send data to the underlying platform?**

    No. This is a read-only API providing metadata about a request.

8. **Do features in this specification enable access to device sensors?**

    No.

9. **Do features in this specification enable new script execution/loading mechanisms?**

    No.

10. **Do features in this specification allow an origin to access other devices?**

    No.

11. **Do features in this specification allow an origin some measure of control over a user agent’s native UI?**

    No.

12. **What temporary identifiers do the features in this specification create or expose to the web?**

    None.

13. **How does this specification distinguish between behavior in first-party and third-party contexts?**

    This feature is restricted to Isolated Contexts (Isolated Web Apps). It is not available to the general web or third-party contexts outside of this specific high-trust environment.

14. **How do the features in this specification work in the context of a browser’s Private Browsing or Incognito mode?**

    Not applicable -- this API is intended to be used in Isolated Web Apps (which do not support either mode).

15. **Does this specification have both "Security Considerations" and "Privacy Considerations" sections?**

    Yes. The explainer includes a "Security and Privacy Considerations" section detailing that the exposed information is already publicly available via raw socket handshakes, and that this API improves security reliability.

16. **Do features in your specification enable origins to downgrade default security protections?**

    No. Conversely, this feature is intended to upgrade security for custom protocols by allowing them to leverage the browser's certificate trust verification.

17. **How does your feature handle non-"fully active" documents?**

    The feature operates via event listeners on a `ControlledFrame`. If the frame or document is not active, the `onHeadersReceived` event will not be processed.

18. **What should this questionnaire have asked?**

    N/A.