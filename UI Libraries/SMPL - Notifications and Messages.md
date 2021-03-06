# Notifications and messages


## Messages
Messages are error, warning or information block display on the page to notify the use.

![](messages.png)

Error messages can be added using the `Messages.add()` method. It assumes that a `Messages` component has been properly instantiated at the page level (See: Preparing the application).

Each message has an `id`. If a message with the same id is already displayed, then it is replaced by the new one. If not, a new message is added to the stack.

    <Button bsStyle="primary" onClick={()=>
        mainForm.getMessages().add({
            key: 1,
            title: "Gasp, an error",
            message: "Next time, choose a different button my friend",
            type: Messages.ERROR
        })}>Display Error</Button>

Messages can be cleared by calling the clear method.

    <Button bsStyle="danger" onClick={()=>
        mainForm.getMessages().clear()
    }>Clear All</Button>

See: `CodeMessages.jsx`

## Dialogs

Darwino features a series of dialogs displaying read-only notifications, alerts, or input dialog, like string input, pickers, custom content (subform)...

See: `CodeMessages.jsx`

![](sampledialogs.png)
