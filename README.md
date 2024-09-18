# inventr-agent.js

This JavaScript module is responsible for managing the Inventr agent.

## Methods

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
  id: 'yourid',
  render: false // Set to true when you want to render
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
window.inventrAgent.setActionAccessToken(
  exampleActionAccessToken
);
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
  )
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

## Including the agent in your HTML

To include the agent in your HTML, use a script tag. The script tag must have an `id` of "inventr-agent". 

Example:

```html
<!-- Including the agent with an id -->
<script id="inventr-agent" src="https://agent.inventr.ai/inventr-agent.js?id=yourid"></script>
```

In this example, the `id` is included as a query parameter in the URL. When the script is loaded, the agent will use this `id` for its operations.

If you want to include the agent but not render it immediately, you can set the `render` parameter to `false`:

```html
<!-- Including the agent with render=false and no id -->
<script id="inventr-agent" src="https://agent.inventr.ai/inventr-agent.js?render=false"></script>
```

Then, you can use JavaScript to render the agent with an `id` when needed:

```javascript
// Render the agent with an id
window.inventrAgent.render({
  id: 'yourid'
});
```

### Waiting for the Agent Script to Load

To ensure that the `window.inventrAgent` object is available before using it, you can wait for the script to load by listening to the `load` event of the script tag:

```html
<!-- Including the agent script -->
<script id="inventr-agent" src="https://agent.inventr.ai/inventr-agent.js?id=yourid"></script>

<script>
  // Wait for the agent script to load
  document.getElementById('inventr-agent').addEventListener('load', function() {
    // Now you can safely use window.inventrAgent
    window.inventrAgent.render({
      id: 'yourid'
    });
  });
</script>
```

### Explanation
- **Script Tag**: The script tag includes the agent script with an `id` of `inventr-agent`.
- **Load Event Listener**: The `load` event listener waits for the script to fully load before using `window.inventrAgent`.
- **Render Method**: The `render` method is called inside the `load` event listener to ensure that the agent is rendered only after the script is loaded.

