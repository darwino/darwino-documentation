# Micro services
Micro services are a simplified form of REST services. A micro service consumes some JSON content as parameters and also produces JSON as a result. This is that simple.

Micro services can be written in different languages, although the `contacts-react` sample shows how to write them in Java.

For more information: https://playground.darwino.com/playground.nsf/ServicesSnippets.xsp

## Creating a micro service
A micro service implements a Java interface called `JsonMicroService`. This interface has one method called when the service is invoked. It features a context argument that contains the information from the requester.

Here is a basic `HelloWorld` service, that takes a greetings parameter and returns a formatted message consuming the parameter:

    public void execute(JsonMicroServiceContext context) throws JsonException {
        Session session = DarwinoContext.get().getSession();
        JsonObject req = (JsonObject)context.getRequest();
        String greetings = req.getString("greetings"); 
        JsonObject result = JsonObject.of("message",StringUtil.format(
            "Hello, {0}. {1}. It is {2} here, on the server!",
            session.getUser().getCn(),
            greetings,
            DateFormatter.getFormat("DEFAULT_TIME").format(new Date())));
        context.setResponse(result);
    }
	
- `Session session = DarwinoContext.get().getSession()`
  Gets the session database for the current user. With this session, the developer can access the data on the behalf of the user.
  
- `String greetings = req.getString("greetings")`
  Reads a parameter from the request. In that case, the request is made of a JSON object with on attribute called 'greetings'.
  
- `context.setResponse(result)`
  Sends the JSON result to the caller.

See: `HelloWorld.java`

### Registering a micro service
Micro services have to be registered using a factory. A unique name has to be assigned to every single micro service. This name will be used by the client when invoking the service.

See: `AppMicroServicesFactory.java`

## Consuming a micro service in JavaScript
Darwino provides a very convenient helper `MicroServices`, to invoke the services. Simply pass the name, the parameters and fetch the result. Then, either get the result or handle the error:

    new MicroServices()
      .name(valid?"HelloWorld":"fake")
      .params({greetings: "I am the React client calling you"})
      .fetch()
      .then((r) => {
        this.setState({
          error: false,
          result: JSON.stringify(r,null,2)
         })
        })
       .catch((e) => {
         this.setState({
           error: true,
           result: e.message+"\n"+e.content
         })
        })

See: `extras/ServicesMicro.java`
