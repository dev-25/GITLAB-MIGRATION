# GitLab Enterprise Edition Upgrade Documentation

**Upgrade From:** GitLab 16.x\
**Upgrade To:** GitLab 18.5.x\
**Upgrade Method:** Incremental Minor Version Upgrade\
**Document Generated On:** 2026-02-20 04:53:28 UTC

------------------------------------------------------------------------

## Overview

This document outlines the step-by-step procedure followed to upgrade
GitLab Enterprise Edition (EE) from version 16.x to 18.5.x.

The upgrade was performed incrementally across required intermediate
versions to ensure database migrations, background migrations, and
package dependencies were applied safely and in supported order.

------------------------------------------------------------------------

# Pre-Upgrade Checklist

-   Take full VM snapshot / backup.
-   Ensure sufficient disk space.
-   Confirm GitLab services are healthy.
-   Ensure no pending background migrations before major upgrades.

------------------------------------------------------------------------

# Upgrade Path Followed

## Step 1 -- Upgrade to GitLab 16.11.x

``` bash
sudo apt install gitlab-ee=16.11.* -y
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
```

Purpose: Upgrade to latest 16.x version before entering GitLab 17.x
series.

------------------------------------------------------------------------

## Step 2 -- Upgrade to GitLab 17.3.x

``` bash
sudo apt install gitlab-ee=17.3.* -y
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
```

------------------------------------------------------------------------

## Step 3 -- Upgrade to GitLab 17.5.x

``` bash
sudo apt install gitlab-ee=17.5.* -y
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
```

------------------------------------------------------------------------

## Step 4 -- Upgrade to GitLab 17.8.x

``` bash
sudo apt install gitlab-ee=17.8.* -y
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
```

------------------------------------------------------------------------

## Step 5 -- Upgrade to GitLab 17.11.x

``` bash
sudo apt install gitlab-ee=17.11.* -y
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
```

Purpose: Final stable release in 17.x before upgrading to GitLab 18.x.

------------------------------------------------------------------------

# Repository Storage Path Modification

During the upgrade process, repository directory location changed.

## Modify GitLab Configuration File

``` bash
sudo vi /etc/gitlab/gitlab.rb
```

Add or update:

``` ruby
gitlab_rails['repositories_storages'] = {
  "default" => {
    "path" => "/data/repositories"
  }
}
```

Purpose: Update repository storage path to new directory location.

------------------------------------------------------------------------

## Apply Configuration Changes

``` bash
sudo gitlab-ctl reconfigure
```

------------------------------------------------------------------------

## Check Background Migrations

``` bash
sudo gitlab-rake gitlab:background_migrations:status
```

Purpose: Ensure no pending background migrations before proceeding to
GitLab 18.x.

------------------------------------------------------------------------

# Upgrade to GitLab 18.x Series

## Step 6 -- Upgrade to GitLab 18.0.x

``` bash
sudo apt install gitlab-ee=18.0.* -y
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
```

------------------------------------------------------------------------

## Step 7 -- Upgrade to GitLab 18.2.x

``` bash
sudo apt install gitlab-ee=18.2.* -y
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
```

------------------------------------------------------------------------

## Step 8 -- Upgrade to GitLab 18.4.x

``` bash
sudo apt install gitlab-ee=18.4.* -y
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
```

------------------------------------------------------------------------

## Step 9 -- Upgrade to GitLab 18.5.x

``` bash
sudo apt install gitlab-ee=18.5.* -y
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
```

------------------------------------------------------------------------

# Final Validation

Verify GitLab version:

``` bash
sudo gitlab-rake gitlab:env:info | grep "GitLab version"
```

Check service status:

``` bash
sudo gitlab-ctl status
```

Monitor logs:

``` bash
sudo gitlab-ctl tail
```

------------------------------------------------------------------------

# Final Status

-   GitLab upgraded successfully from 16.x to 18.5.x
-   Incremental upgrade path followed correctly
-   Repository storage path updated
-   Background migrations verified before major upgrade
-   Services reconfigured and restarted after each version

------------------------------------------------------------------------
