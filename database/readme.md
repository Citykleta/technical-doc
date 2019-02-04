# Database

The database is currently deployed on a [Digital ocean droplet](https://digitalocean.com) (costing 5$/month) within a docker stack.
The database container already embed PostGIS extension.

Alongside with the database service come:
1. A service to sync the database with the latest version of Open Street Map data.
2. A service to gather data from the database and upload a custom tileset to Mapbox.

The two latter are meant to be ephemeral and can be triggered in various ways:
- With a command line when logged in in the droplet with SSH.
- A CRON job deployed to the droplet to schedule regular updates
- Maybe (to be checked?) through digital ocean API call

For now, the database does not hold custom data and is a simple replicate of Open Street Map data.

Later when connected to our API and owning some users data the configuration should be audited especially for security considerations including among others:
- Setting up different users and roles
- forcing ssl connection as the database may not be part of private network
- implementing a backup strategy
