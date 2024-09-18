# inventr-agent.js

This JavaScript module is responsible for managing the Inventr agent.

## Including the agent in your HTML

To include the agent in your HTML, use a script tag. The script tag must have an `id` of "inventr-agent".

Example:

```html
<!-- Including the agent with an id -->
<script
  id="inventr-agent"
  src="https://agent.inventr.ai/inventr-agent.js?id=yourid"
></script>
```

In this example, the `id` is included as a query parameter in the URL. When the script is loaded, the agent will use this `id` for its operations.

If you want to include the agent but not render it immediately, you can set the `render` parameter to `false`:

```html
<!-- Including the agent with render=false and no id -->
<script
  id="inventr-agent"
  src="https://agent.inventr.ai/inventr-agent.js?render=false"
></script>
```

Then, you can use JavaScript to render the agent with an `id` when needed:

```javascript
// Render the agent with an id
window.inventrAgent.render({
  id: "yourid",
});
```

### Waiting for the Agent Script to Load

To ensure that the `window.inventrAgent` object is available before using it, you can wait for the script to load by listening to the `load` event of the script tag:

```html
<!-- Including the agent script -->
<script
  id="inventr-agent"
  src="https://agent.inventr.ai/inventr-agent.js?id=yourid"
></script>

<script>
  // Wait for the agent script to load
  document
    .getElementById("inventr-agent")
    .addEventListener("load", function () {
      // Now you can safely use window.inventrAgent
      window.inventrAgent.render({
        id: "yourid",
      });
    });
</script>
```

#### Explanation

- **Script Tag**: The script tag includes the agent script with an `id` of `inventr-agent`.
- **Load Event Listener**: The `load` event listener waits for the script to fully load before using `window.inventrAgent`.
- **Render Method**: The `render` method is called inside the `load` event listener to ensure that the agent is rendered only after the script is loaded.

## Using the inventrAgent Programmatically in JavaScript

Using the inventrAgent Programmatically in JavaScript
The inventrAgent provides a set of methods that allow you to integrate and control the chatbot interface within your web application. This section describes how to use these methods to render the chatbot and handle action callbacks.

### Methods

### render(options)

Renders the chatbot interface. The `options` object can include:

- `fullscreen`: If true, the chatbot displays in fullscreen mode.
- `collapse`: If true, the chatbot starts in a collapsed state.
- `dev`: If true, the chatbot connects to the development server.
- `id` or `agentAccessKey`: The access key for the agent.
- `render`: If false, the chatbot does not render immediately.

These options can also be passed as URL parameters when loading the agent:

```
https://agent.inventr.ai/inventr-agent.js?id=yourid&fullscreen&collapse&dev&render=false
```

In this URL, `fullscreen`, `collapse`, and `dev` are valueless parameters, which are treated as `true`. The `render` parameter is set to `false`, so the chatbot does not render immediately.

Example:

```javascript
window.inventrAgent.render({
  fullscreen: true,
  collapse: true,
  dev: true,
  id: "yourid",
  render: false, // Set to true when you want to render
});
```

<!-- ### addActionCallback(action, callback, data)

Registers a callback function for a specific action. The `action` is a string that specifies the action, and the `callback` is a function that is called when the action is received. The function also accepts an optional `data` object that is sent to the server when the callback is registered. The function returns an object with a `remove` method that can be used to remove the callback.

Example:

```javascript
const callback = window.inventrAgent.addActionCallback('action', function(data) {
  console.log('Received action with data:', data);
});
// To remove the callback:
callback.remove();
``` -->

### setActionAccessToken(token)

Sets the action access token, which is used to authorize calls between the client and the agent based on the client's provided `actionCallbackUrl`. The token should be something secure like a JWT so that authentication can be performed on the client server. The token is passed in the header `Inventr-agent-action-access-token` for `actionCallbackUrl` requests.

#### Example Usage

```javascript
const exampleActionAccessToken = btoa(
  JSON.stringify({ userId: "1", clientId: "2" })
);
window.inventrAgent.setActionAccessToken(exampleActionAccessToken);
```

#### Parameters

- **token**: The action access token, secure token (JWT or other) that can be used to validate action requests.

### addActionListener(actionName, callback)

Registers a listener for a specific action. The `actionName` is a string that specifies the action, and the `callback` is a function that is called when the action is completed. The event object is passed as an argument to this function.

#### Example Usage

```javascript
// Add an action listener for the DELETE_WORKSPACE action
window.inventrAgent.addActionListener("DELETE_WORKSPACE", (workspace) => {
  // Custom code to handle the DELETE_WORKSPACE action
  console.log("DELETE_WORKSPACE", workspaces.name, workspaces.value);
});
// Add an action listener for the DELETE_WORKSPACE action
window.inventrAgent.addActionListener("LIST_WORKSPACES", (workspaces) => {
  // Custom code to handle the LIST_WORKSPACES action
  workspaces.forEach((workspace) =>
    console.log("LIST_WORKSPACES", workspace.name, workspace.value)
  );
});
```

#### Parameters

- **actionName**: The name of the action to listen for.
- **callback**: The function to execute when the action is performed. The event object is passed as an argument to this function.

### restore()

Restores the chatbot to its normal state from either fullscreen or collapsed state.

Example:

```javascript
window.inventrAgent.restore();
```

### collapse()

Collapses the chatbot to a minimized state.

Example:

```javascript
window.inventrAgent.collapse();
```

### fullscreen()

Puts the chatbot into fullscreen mode.

Example:

```javascript
window.inventrAgent.fullscreen();
```

### remove()

Removes the chatbot interface from the document.

Example:

```javascript
window.inventrAgent.remove();
```

## Action callbacks

### How Action Callbacks Work with Inventr Agent

The Inventr Agent uses action callbacks to communicate with the web server and perform specific actions. The client provides an actionCallbackUrl to the agent, which the agent calls to execute actions. The actions are authorized using a secure token, typically a JWT, which is supplied by the web app to the agent and passed in the header `Inventr-Agent-Action-Access-Token`.

Action callbacks to the client's server from the Inventr Agent give the ability to use chat or natural voice to control data in the application. This is set up with the actionCallbackUrl for the agent. An action access token (JWT, for example) can be set by the client's web application. Actions can be used to authorize and/or manipulate data. A listener can be added to the client web application (`addActionListener`) to perform validated actions on the web application.

#### ActionCallbackUrl Express Example

This example demonstrates how to set up an Express route to handle action callbacks from the Inventr Agent. The route listens for POST requests at the specified `actionCallbackUrl` and performs various actions based on the request data.

### Example Code

```javascript
const express = require("express");
const app = express();

// Add JSON body parser
app.use(express.json());

// Add the actionCallbackUrl route
// Example actionCallbackUrl: https://www.myapp.com/agent/action
app.use("/agent/action", async (req, res) => {
  // Get the actionAccessToken from the request headers
  // The token is supplied by the web app to the agent
  const actionAccessToken = req.headers["inventr-agent-action-access-token"];
  const tokenVals = actionAccessToken ? decryptToken(actionAccessToken) : null;
  if (!tokenVals) return res.status(401).send("Invalid token");

  // Get the action name and value from the request body
  const { name, value } = req.body;

  // Perform the action
  let ret = { success: false, value: null };
  switch (name) {
    case "LIST_WORKSPACES":
      // Get the list of workspaces
      if (!checkUserAuth(tokenVals.userId, "workspace", "view"))
        return res.status(401).send("Unauthorized");
      ret.success = true;
      ret.value = await fetchWorkspaces(
        { userIds: tokenVals.userId },
        { fields: ["id", "name"] }
      );
      break;
    case "GET_WORKSPACE":
      // Get the workspace details
      if (!checkUserAuth(tokenVals.userId, "workspace", "view"))
        return res.status(401).send("Unauthorized");
      ret.success = true;
      ret.value = await fetchWorkspace(
        { id: value.id, userId: tokenVals.userId },
        { fields: ["id", "name", "description", "active"] }
      );
      break;
    case "UPDATE_WORKSPACE":
      // Update the workspace
      if (!checkUserAuth(tokenVals.userId, "workspace", "update"))
        return res.status(401).send("Unauthorized");
      ret.success = await updateWorkspace(
        { id: value.id, userId: tokenVals.userId },
        {
          name: value.name,
          description: value.description,
          active: value.active,
        }
      );
      ret.value = { name: value.name, id: value.id };
      break;
    case "CREATE_WORKSPACE":
      // Create the workspace
      if (!checkUserAuth(tokenVals.userId, "workspace", "create"))
        return res.status(401).send("Unauthorized");
      const workspace = await createWorkspace({
        userId: tokenVals.userId,
        name: value.name,
        description: value.description,
      });
      ret.success = workspace ? true : false;
      ret.value = { name: workspace.name, id: workspace.id };
      break;
    case "DELETE_WORKSPACE":
      // Delete the workspace
      if (!checkUserAuth(tokenVals.userId, "workspace", "delete"))
        return res.status(401).send("Unauthorized");
      ret.success = await deleteWorkspace({
        id: value.id,
        userId: tokenVals.userId,
      });
      ret.value = { name: value.name, id: value.id };
      break;
    default:
      return res.status(400).send("Invalid action name");
  }

  // Return the response
  res.status(200).json(ret);
});

const port = 3000;
app.listen(port, () => {
  console.log(`Example app listening on port ${port}`);
});
```

### Explanation

1. **Route Setup**: The route `/agent/action` is defined to handle POST requests. This is the `actionCallbackUrl` that the Inventr Agent will call to perform actions.

2. **Token Extraction and Validation**:

   - The `actionAccessToken` is extracted from the request headers using the header name `inventr-agent-action-access-token`.
   - The token is decrypted using the `decryptToken` function. If the token is invalid or missing, a `401 Unauthorized` response is sent.

3. **Action Handling**:

   - The action name and value are extracted from the request body.
   - A switch statement is used to handle different actions based on the `name` field.
   - For each action, the user's authorization is checked using the `checkUserAuth` function.
   - Depending on the action, different functions are called to perform the required operations (e.g., `fetchWorkspaces`, `fetchWorkspace`, `updateWorkspace`, `createWorkspace`, `deleteWorkspace`).

4. **Response**:
   - A response object `ret` is constructed with the result of the action.
   - The response is sent back to the client with a `200 OK` status and the result in JSON format.

### Actions Supported

- **LIST_WORKSPACES**: Retrieves a list of workspaces for the authenticated user.
- **GET_WORKSPACE**: Retrieves details of a specific workspace.
- **UPDATE_WORKSPACE**: Updates the details of a specific workspace.
- **CREATE_WORKSPACE**: Creates a new workspace.
- **DELETE_WORKSPACE**: Deletes a specific workspace.

### Security

- The `actionAccessToken` should be a secure token like a JWT, which is passed in the header `inventr-agent-action-access-token`.
- The token is used to authenticate and authorize the actions performed by the client on the server.

This setup ensures that only authorized actions are performed and that the actions are securely authenticated using the provided token.
