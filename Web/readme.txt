Unable to configure HTTPS endpoint. No server certificate was specified, and the default developer certificate could not be found

dotnet dev-certs https --clean
dotnet dev-certs https --trust
Restart VS