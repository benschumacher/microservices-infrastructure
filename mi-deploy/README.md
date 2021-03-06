## MI-Deploy

This tool automates the deployment of Microservices Infrastructure to a variety
of platforms, currently including
 - [x] AWS
 - [x] DigitalOcean
 - [x] GCE
 - [x] Openstack
 - [x] Vagrant
 - [x] SoftLayer

It does this by creating a fresh, barebones clone of the repo from the selected
branch, copying in necessary authentication and terraform data, and running
Anisble and Terraform. It attempts to make this whole process more user friendly
and easy, while catching errors and attempting fixes.

### Installation and Usage

#### Installation

You can download premade binaries of mi-deploy from our [Bintray][bintray],
under the "Versions" tab.

This tool can be built with the `go` tool, with a simple
```bash
go get . && go build .
```

Deployment requires a set of files that are unique to the way you deploy. In
particular, your directory tree should look something like this:
```
$ tree .
.
├── deployments
│   └── ...
├── mi-deploy.go
├── prompt
│   └── ...
├── README.md
├── remote.sh           # Optional, example
├── sh
│   └── ...
└── tf-files
    ├── account.json
    ├── aws.tf
    ├── digitalocean.tf
    ├── softlayer.tf
    ├── gce.tf
    ├── openstack.tf
    ├── security.yml
    ├── ssl             # Generated by security-setup
    │   └── ...
    └── terraform.yml
```

#### Usage

Once you have a binary, you can use the `--help` flag to show the help text for
any subcommand
```bash
$ ./mi-deploy --help
...
USAGE:
   mi-deploy [global options] command [command options] [arguments...]

VERSION:
   0.0.1-dev

COMMANDS:
   deploy   deploy a branch to a platform
   destroy  destroy the resources and files from a deployment
   help, h  Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --help, -h       show help
   --version, -v    print the version
```

Example usage:
```bash
$ ./mi-deploy deploy --platform aws --branch master
$ ./mi-deploy --verbosity=debug deploy -p vagrant -b fix/bug
$ ./mi-deploy destroy
$ ./mi-deploy destroy --filter "vagrant"
```

### Details

Calling deploy will create a new directory under `deployments/` which is named
by platform, branch, and timestamp. Your authentication data will then be copied
from `./tf-files/` into that clone, and Terraform and Ansible will be run
sequentially.

Upon encountering an error, mi-deploy attempts to provide useful options for
continuation. This often requires keyboard input. This script is designed to be
run manually, from a TTY (otherwise, it would be a simple bash script ;) ).

When destroying, the terraformed resources are destroyed and the directory is
deleted.

### Gotchas

This program copies a few files from `./tf-files/` into the repo automatically,
and if these change, it can cause problems. Keep an eye out for differences in
`terraform.yml`, `security-setup`, and `ssl/`.

### Deploying From a Remote Machine

Using this program on a remote machine is easy! An example script is
provided in remote.sh.

### Roadmap

Hopefully, this program will pave the way for and implement some form of
automated building and testing of MI clusters. The big TODO item is automated
testing, to be integrated with something like Jenkins.

TODO:
 * Better test coverage
 * More automation of setup (run security-setup, etc)
 * Automated testing
   - `curl` Consul health endpoints
   - Reboot hosts

### License

This is considered to be a part of Microservices-Infrastructure, and is under
the same licensing.

[bintray]: https://bintray.com/asteris/mantl-deploy/mantl-deploy/view
