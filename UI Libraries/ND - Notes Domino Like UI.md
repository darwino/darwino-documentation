# Notes/Domino like UI

The Darwino UI library can be used to create UIs closed to Notes/Domino ones. They help with the migration of existing applications.
More features will be provided other time in these classes to fulfill the needs of migration projects.

## Specific library
Some Notes/Domino like features are provider in a specific library `@darwino/darwino-react-bootstrap-notes`. You should include this library to get access to these features

## Forms and subforms
The library provides some Notes/Domino specific classes for forms and subforms.
- FormPage: base form class
    - Provides an action bar that the form, and subforms, can contribute to
    - Sends the computed fields to the server, as a transient property, when the document is saved. This allows server side processing to use these values.
- Subform: base subform class

## ViewPage & ViewGrid
Base class for displaying views that can even be embedded within a form. In that case, such an 'embedded view' can contribute to the form action bar.

## Dynamic routing
In Notes/Domino, the form associated with a document is deduced from the FORM field. To emulate this behavior, the ViewGrid features a `dynamicRoute` property pointing to a function. This function should return the route path for a view entry. Here is an implementation reading the form field and deducing the path:

    function RouteForm(entry) {
        let form = entry && entry.form;
        if(!form) return null;
        let id = entry ? entry.__meta.unid : "";
        switch(form) {
            case "Contact":     return "/app/contact/"+id;
            case "Company":     return "/app/company/"+id;
        }
        return null;
    }

See: `notes/AllContacts.jsx`
See: `RouteForm.jsx`
