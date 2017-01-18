# bosh-add-on-search-domain

## install bosh-lite

Bring bosh-lite VM up with Vagrant:
```
$ vagrant init cloudfoundry/bosh-lite

$ vagrant up --provider=virtualbox

Bringing machine 'default' up with 'virtualbox' provider...
```

## install bosh cli
BOSH CLI is a command line interface to the _Director_. It is distributed as a Ruby gem.
```
$ gem install bosh_cli
```

Let CLI know about bosh Director (use admin/admin to log in):
```
$ bosh target <director_url>

```
Note: <director_url> would be the URL or IP address of bosh director

## clone repository
```
$ git clone https://github.com/phopper-pivotal/bosh-add-on-search-domain.git

$ cd bosh-add-on-search-domain
```

## create bosh release
Let's create a bosh release using the project included with this repository. When prompted for "development release name" use _search_domain_

```
$ create bosh release --force 
[WARNING] Missing blobstore configuration, please update config/final.yml before making a final release
Syncing blobs...
Please enter development release name: search_domain

Building DEV release
---------------------------------
Release artifact cache: /Users/phopper/.bosh/cache

Building license
----------------
Building license...
  Warning: Missing LICENSE or NOTICE in /Users/phopper/Documents/pivotal/bosh/bosh-add-on-search-domain


Building packages
-----------------

Building jobs
-------------
Building search_domain...
  No artifact found for search_domain
  Generating...
  Generated version '6b3067919b3bea0314817abc0eb201bc70d35eb8'


Building release
----------------

Generating manifest...
----------------------
Writing manifest...

Release summary
---------------
Packages
+------+---------+-------+
| Name | Version | Notes |
+------+---------+-------+
+------+---------+-------+

Jobs
+---------------+------------------------------------------+-------------+
| Name          | Version                                  | Notes       |
+---------------+------------------------------------------+-------------+
| search_domain | 6b3067919b3bea0314817abc0eb201bc70d35eb8 | new version |
+---------------+------------------------------------------+-------------+

Jobs affected by changes in this release
+---------------+------------------------------------------+
| Name          | Version                                  |
+---------------+------------------------------------------+
| search_domain | 6b3067919b3bea0314817abc0eb201bc70d35eb8 |
+---------------+------------------------------------------+

Release name: search_domain
Release version: 0+dev.1
Release manifest: /Users/phopper/Documents/pivotal/bosh/bosh-add-on-search-domain/dev_releases/search_domain/search_domain-0+dev.1.yml
```

## upload release to bosh director
Now that we have a bosh release, we need to upload it to bosh director for use as a bosh add on;
```
bosh upload release dev_releases/search_domain/search_domain-0+dev.1.yml                                                           1 ↵
Acting as user 'admin' on 'Bosh Lite Director'
[WARNING] Missing blobstore configuration, please update config/final.yml before making a final release

Copying packages
----------------


Copying jobs
------------
search_domain

Generated /var/folders/hz/_9jdwmpn39n1dmyfj2nlw0880000gn/T/d20170118-56634-1ijw7i3/d20170118-56634-wjto1r/release.tgz
Release size: 888B

Verifying manifest...
Extract manifest                                             OK
Manifest exists                                              OK
Release name/version                                         OK


Uploading release
release.tgz:    96% |ooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo    |    861B  39.5KB/s ETA:  00:00:00
Director task 42
  Started extracting release > Extracting release. Done (00:00:00)

  Started verifying manifest > Verifying manifest. Done (00:00:00)

  Started resolving package dependencies > Resolving package dependencies. Done (00:00:00)

  Started processing 1 existing job > Processing 1 existing job. Done (00:00:00)

  Started release has been created > search_domain/0+dev.4. Done (00:00:00)

Task 42 done

Started		2017-01-18 20:39:23 UTC
Finished	2017-01-18 20:39:23 UTC
Duration	00:00:00
release.tgz:    96% |ooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo    |    861B    785B/s Time: 00:00:01

Release uploaded
```

## verify releases
We can verify the release has been uploaded to bosh director;
```
$ bosh releases
Acting as user 'admin' on 'Bosh Lite Director'

+---------------+----------+-------------+
| Name          | Versions | Commit Hash |
+---------------+----------+-------------+
| learn-bosh    | 0+dev.3* | fd909331+   |
| search_domain | 0+dev.1  | f7826eb7+   |
+---------------+----------+-------------+
(*) Currently deployed
(+) Uncommitted changes

Releases total: 2
```

## update runtime-config for bosh add on
We have a release uploaded, now let's update the runtime-config for the add on
```
$ bosh update runtime-config service-domain.yml
Acting as user 'admin' on 'Bosh Lite Director'
Successfully updated runtime config
```

## deploy add on to the VMs
```
$ bosh deploy
Acting as user 'admin' on deployment 'learn-bosh' on 'Bosh Lite Director'
Getting deployment properties from director...

Detecting deployment changes
----------------------------
releases:
- name: search_domain
  version: 0+dev.1

addons:
- name: add-search-domain
  jobs:
  - name: search_domain
    release: search_domain

properties:
  search_domain: "<redacted>"
Please review all changes carefully

Deploying
---------
Are you sure you want to deploy? (type 'yes' to continue): yes

Director task 26
Deprecation: Ignoring cloud config. Manifest contains 'networks' section.

  Started preparing deployment > Preparing deployment. Done (00:00:00)

  Started preparing package compilation > Finding packages to compile. Done (00:00:00)

  Started updating job app > app/0 (6665a166-4c65-4d07-9144-fc0f9a028e6e) (canary). Done (00:01:25)

Task 26 done

Started		2017-01-18 20:26:33 UTC
Finished	2017-01-18 20:27:58 UTC
Duration	00:01:25

Deployed 'learn-bosh' to 'Bosh Lite Director'
```