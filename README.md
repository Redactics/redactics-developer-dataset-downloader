# Redactics Developer Dataset Download Recipe

The following are some sample instructions you can tailor for use to download and apply Redactics datasets to your local developer environments

### One-time Install/Setup Instructions

1. Copy the `docker-compose-redactics.yml` file in this repo to your own
1. If you haven't already, download and install the [AWS CLI](https://aws.amazon.com/cli/). Run `aws configure` to setup the CLI using your AWS access key ID and secret access key you'll need to get from your AWS account owner, if you don't already have this information. You can test that the AWS CLI is setup and authenticated to your environment correctly by running `aws s3 ls`. If this doesn't return an error you can proceed to the next steps.

### Download and Apply a Redactics Dataset

The following are some sample instructions you can tweak as needed to suit your purposes. If you are using Windows these instructions assume you are running this from within a WSL Linux environment (e.g. Ubuntu):

1. Run `docker-compose -f docker-compose-redactics.yml run downloader s3 cp s3://[your bucket name]/[your Redactics exported filename] /tmp/downloads`. This will copy `[your Redactics exported filename]` (e.g. `complete-dump.sql`) to the /tmp folder on your workstation.
1. Make this file available to your database. If you are using Docker and your PostgreSQL service is called `postgres` you can add a volume mapping such as: `- /tmp:/tmp` to map your /tmp folder into your container.
1. Restore from this file. If it is an `.sql` file and not a `.csv` you can do so by running: `pg_restore -c --if-exists -O -d [your database] -h [your host] -U [your postgres username] -W /tmp/[your Redactics exported filename]` (e.g. `complete-dump.sql`). The `-c` and `--if-exists` option assures this data is clean installed. If your PostgreSQL connection info is passed in as environment variables you can omit the `-d [your database]`, `-h [your host]`, `-U [your postgres username]`, and `-W` flag arguments. Full information on the pg_restore command can be found [here](https://www.postgresql.org/docs/12/app-pgrestore.html).
1. You will probably also want to run a query to restore your user account with your proper email address so you can login as yourself, assuming your email address was altered by your Redactics workflow.
1. You might want to combine all of these commands into your own bash script, e.g.
  ```
  #!/bin/bash
  set -eo pipefail
  docker-compose -f docker-compose-redactics.yml ...
  ```
  You'll need to `chmod +x /path/to/your/script.sh` before running it.
