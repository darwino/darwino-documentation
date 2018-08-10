Basic NPM Settings
-----------------

Darwino requires NodeJS and NPM to compile the new generation of JavaScript applications.

Release versions of the Darwino JavaScript assets are distributed through the central NPM registry and don't require any special configuration.

### Development Builds

Incremental development builds are distributed through a private NPM server serving all the packages scoped by @darwino. For normal use, this is not necessary, but it may be useful if you have an existing project using "snapshot" builds or are working with the Darwino development team on upcoming features.

To be able to consume them, you should instruct you local NPM to use that server for @darwino. This is done by running the following command from the command line (it assumes that NodeJS and NPM are installed):
`npm adduser --registry https://npm.darwino.com --scope @darwino`

It will ask you for your darwino user/password and your email. Once done, you'll be able to consume all the darwino packages as described at: [https://npm.darwino.com](https://npm.darwino.com)
