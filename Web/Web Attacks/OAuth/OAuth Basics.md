OAuth is a commonly used authorization framework that enables websites and web applications to request limited access to a user's account on another application. Crucially, OAuth allows the user to grant this access without exposing their login credentials to the requesting application. This means users can fine-tune which data they want to share rather than having to hand over full control of their account to a third party.

The basic OAuth process is widely used to integrate third-party functionality that requires access to certain data from a user's account. For example, an application might use OAuth to request access to your email contacts list so that it can suggest people to connect with. However, the same mechanism is also used to provide third-party authentication services, allowing users to log in with an account that they have with a different website.

## How does OAuth 2.0 work?

OAuth 2.0 was originally developed as a way of sharing access to specific data between applications. It works by defining a series of interactions between three distinct parties, namely a client application, a resource owner, and the OAuth service provider.

-   **Client application** - The website or web application that wants to access the user's data.
-   **Resource owner** - The user whose data the client application wants to access.
-   **OAuth service provider** - The website or application that controls the user's data and access to it. They support OAuth by providing an API for interacting with both an authorization server and a resource server.

There are numerous different ways that the actual OAuth process can be implemented. These are known as OAuth "flows" or "grant types". In this topic, we'll focus on the "authorization code" and "implicit" grant types as these are by far the most common. Broadly speaking, both of these grant types involve the following stages:

1.  The client application requests access to a subset of the user's data, specifying which grant type they want to use and what kind of access they want.
2.  The user is prompted to log in to the OAuth service and explicitly give their consent for the requested access.
3.  The client application receives a unique access token that proves they have permission from the user to access the requested data. Exactly how this happens varies significantly depending on the grant type.
4.  The client application uses this access token to make API calls fetching the relevant data from the resource server.