# Actions and action bars

The ViewPage, as well as the DocumentForm class can gather actions and eventually display them in an action bar. These actions are contributed through a `contributeActionBar()` function, implemented by the form, subform, viewgrid object.

## Displaying the action bar

The pages don't automatically create the actionbar as it cannot preclude the desired UI and its location. That actionbar should be explicitly created by the page, in its render method:

    render() {
      return (
        <div>
          <form onSubmit={handleSubmit(this.handleUpdateDocument)}>
            {this.createActionBar()}

Note: the Notes/Domino specific base class for form and view create this action bar, as it mimics Notes/Domino UI.

## 