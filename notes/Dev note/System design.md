### Ref

[GitHub - weeeBox/mobile-system-design: A simple framework for mobile system design interviews](https://github.com/weeeBox/mobile-system-design#functional-requirements)

# Understand the problem

The interviewer defines the task. 

For example: *"Design Twitter Feed"*

- First step is to understand the scope:
    - **Client-side only** - just a client-app: you have the backend and API available.
    - **Client-side + API** - likely choice for most interviews: you need to design a client app and API.
    - **(Less likely) Client-side + API + Back-end** - less likely choice since most mobile engineers would not have a proper backend experience. If the interviewer asks server-side questions - let them know that you're most comfortable with the client-side and don't have hands-on experience with backend infrastructure. It's better to be honest than trying to fake your knowledge. If they still persist - let them know that everything you know comes from books, YouTube videos, and blog posts.

# Requirement gathering

Task requirements can be split into :

- **functional**
- **non-functional**
- **Out of scope**

System design questions are made ambiguous. The interviewer is more interested in seeing your thought process than the actual solution you produce:

1. What kind of question you are frequently asked?
2. What assumptions did you make and how did you state them?
3. What concerns and trade-offs did you mention?

### **Functional requirements**

Think about 3–5 features that would bring *the biggest value to the business.*

1. Does the newest story appear on the top?
2. Should the list be an infinite scroll list?
3. User is able to like, comment and unfold the story.
4. User is able to share, save the story.

### **Non-functional requirements**

1. Offline support
2. i18n needed
3. Image cache
4. skeleton view/feed cache
5. Real-time notifications(Push notifications). WebSocket required.
6. Are we having deadline?

### **Out of scope**

1. Login/Authentication included
2. Feed is able to embedded in to other application(web/mobile) and can be redirect to our app through deeplink.
3. Data tracking

## **Clarifying Questions**

The interviewer is focus on what clarifying questions are asked.

For example:

1. Are we going to support emerging markets?

Publishing in different country brings additional challenges. Don’t mention that we have to translate it into their language. The app size should be as small as possible due to low-end device and poor network condition. The app itself should limit the network request and use caching heavily.

1. How many user do we expect?

Enormous amount of network requests might cause backend server down. 

Hacker can easily initiate a DDoS attack on the server.

- How do the server handle the exponential-growing user base?
- Do we have any api rate-limit?
- Do we have backup for our down server?
1. (Senior question) How big is the development team?

This question is relatively senior. Because this means you are the designer of this system. You can manage people for working in your system.

# **High-Level Diagram**

After gathering the requirements and clarifying the questions, you can ask the interviewer if they want to see some high-level diagram. For example:

Because the acceptance criteria is to implement these requirements:

- Twitter feed(infinite scroll behavior, like tweet)

I think we can start with `Twitter feed flow` . We use the block to represent the abstraction of feed flow.

Then, we maybe need to a service to communicate with the backend, and local storage as well.

including `Network service` and `persistent storage.`

We may want a Dependency injection to manage the dependencies, which is `DI graph(container)`.

And we may need some helper to help us bridge the feed flow to the app module.

`helper` .

This is just the basic. I think For a mid-level Engineer. Th system design round might be heavy on the implementation side. For example, we might need to define the protocol of each component to figure it out what does each component work and the data model as well. How do you design your Twitter feed data model (question might have: why do you choose struct over class?)