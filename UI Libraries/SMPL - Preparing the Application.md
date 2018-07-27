# Preparing the application

## Displaying dialogs
To ease the display of dialogs, like notifications or pickers, the main component of the 
application should add a `Dialog` component that will then be used for display. This is an initially hidden component, consumed by the core runtime.
Here is what the main layout page should contain:

    <Dialog/>

See: `Layout.jsx`

## Notification messages
Similarly to dialogs, the application can display message blocks, like notifications, alerts, ... This requires a `<Messages>` component to be added to the page. Although it can be provided at the global level, like the Dialog above, it is generally better to make it part of the every single page, so the developer control where these messages will appear:

The base Form class provides a helper, `createMessages()`, to ease the creation of the messages:

        return (
            <div>
                <form onSubmit={handleSubmit(this.handleUpdateDocument)}>
                    {this.createMessages()}
