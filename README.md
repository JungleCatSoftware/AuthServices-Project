# AuthServices-Project
Repo for testing and developing the AuthServices project.

## Dependencies
Dependencies to build/run the project from this repo. Older (or newer) versions may work, but these versions are confirmed to work.

- Git (2.5.0)
- Vagrant (1.8.1)
- Vagrant plugins:
  - vagrant-r10k (0.4.1)
  - puppet (4.3.2)
- VirtualBox (5.0.14r105127)

## Usage
### Running
**NOTE:** Ensure all submodules are pulled down or this will fail to configure. Cloning with the `--recursive` flag or running `git submodule update --init --recursive` will ensure all dependant submodules are retrieved.  

To launch the code run `vagrant up` from the repository root; this will launch and configure the necessary VMs in Virtualbox. This can take some time, especially the first time this is run, but will be faster for subsequent runs due to sharing the apt cache through Vagrant and Puppet module caching by r10k.

A virtual host-only interface will be created on the host system which will allow Nginx on the webproxy VM to be accessed at 10.10.2.10. It is recommended (but not necessary) to edit `/etc/hosts` to map *authservices.example.net* and *api.authservices.example.net* to this IP address. 

### Restarting Services / Applying Code Updates
To apply Python code changes, use `vagrant ssh authservicesweb` or `vagrant ssh authservicesapi` to log in to the app server for the app with changes and run `sudo /etc/init.d/gunicorn restart` to restart Gunicorn and the Python application.

If dependencies for the application have changed, run `vagrant provision` to run the Puppet provisioning steps again and install/update the Python dependencies. Restart the app with the previous commands if necessary.

### Stopping / Teardown
To destroy the environment (either when done or to be able to test rebuilding the environment) use `vagrant destroy -f` to terminate the VMs.
