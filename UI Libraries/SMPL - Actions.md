# Actions and action bars


The Viewgrid and the 
    contributeActionBar() {
        return (
            <div key="main">
                <Link to={`${this.props.baseRoute}`} className="btn btn-primary">Create New Contact</Link>
                <Button onClick={() => {this.export('csv')}}>Export</Button>
            </div>
        );
    }
                    {this.createActionBar()}
                    {this.createMessages()}
