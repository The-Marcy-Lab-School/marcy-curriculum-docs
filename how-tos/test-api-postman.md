# Testing With Postman

[Postman](https://www.postman.com/) is a powerful API platform that allows developers to design, build, test, and collaborate on APIs, offering features like request sending, response viewing, documentation generation.

The VS Code extension makes it incredibly easy to test your API without leaving VS Code!

* Create an account on [Postman.com](https://www.postman.com/)
* Download the Postman VS Code extension and sign in to your account.
* Create a collection and name it.

![A collection on Postman is used to organize API calls for a single project](img/postman-collections.png)

* Add a request for each of your endpoints:
  * `GET /api/fellows`
  * `GET /api/fellows/:id`
  * `POST /api/fellows`
  * `PATCH /api/fellows/:id`
  * `DELETE /api/fellows/:id`
* Requests that require a body, select the **body** tab, then **raw**, and choose **JSON** from the type dropdown.
* Then, test out your server's endpoints using Postman

![A POST request with a JSON body is received by the server and a response is sent. Postman displays the response.](img/postman-testing.png)
