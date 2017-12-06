# Preparing the application

## Displaying dialogs
To ease the display of dialogs, like notifications of pickers, the main component of the 
application should add a `Dialog` component that will then be used for display.

    <Dialog/>

See: `Layout.jsx`

## Notification messages
Similarly to dialogs, the application can display messages like notification, alerts, ... This requires a `<Messages>` component to be added to the page. It can be don at the global level, like the dialog, or at the page level. It actually depends where the messages should be displyed

The base Form class provides a helper, `createMessages()`, to ease the creation of the messages:

        return (
            <div>
                <form onSubmit={handleSubmit(this.handleUpdateDocument)}>
                    {this.createMessages()}
