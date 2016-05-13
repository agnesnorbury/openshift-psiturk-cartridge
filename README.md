## OpenShift Psiturk Cartridge

This is a fork of the [OpenShift Advanced Python Cartridge](https://github.com/gsterjov/openshift-advanced-python-cartridge) with some minimal configuration changes that make it amenable for hosting a [psiturk](https://github.com/NYUCCL/psiTurk) experiment. 

The advantages of using this cartridge over the setup described on psiturk's [readthedocs](http://psiturk.readthedocs.io/en/latest/openshift.html) are that:

1. you get an Nginx front server which is much faster for serving your static content than is the standaline gunicorn server
2. You can host your own ad
3. The psiturk server starts up and runs automatically without you having to log in and start/stop it

The main disadvantage is that you can't install it using the openshift web gui -- you'll need to install the `rhc` commandline tools to install it. Instructions on doing that [here](https://developers.openshift.com/managing-your-applications/client-tools.html).

It doesn't actually run the psiturk custom gunicorn server, it uses its own gunicorn server. But that's okay. The biggest consequence of this is that your server logs won't be in the same place -- they'll instead be in the `~/advanced-python/logs` dir on OpenShift.

### Installation

To install this cartridge use the cartridge reflector when creating an app

	rhc create-app myapp http://cartreflect-claytondev.rhcloud.com/reflect?github=deargle/openshift-advanced-python-cartridge-e OPENSHIFT_PYTHON_SERVER=gunicorn


### Usage

You can still log in via `ssh` to the OpenShift server and start the `psiturk` shell to administrate the creation of HITs or whatever. Or you can run the `psiturk` shell from your own computer to do the administration, it doesn't make a difference as long as your AMT credentials are in place. Know though that if you start the psiturk server via `server on` that that gunicorn server won't be accessible on OpenShift because it doesn't bind to the correct port. Solution? Just don't use the `psiturk` gunicorn server! There's already one running that will server your requests. 

The templated `requirements.txt` contains an entry for the github @master version of `psiturk`, so if you 

- `git add requirements.txt` 
- `git commit`
- `git push` 

this file, `psiturk` will be added to your OpenShift python virtual environment and you'll be able to start the `psiturk` shell the next time you log in.


#### Environment Variables

<code>OPENSHIFT_PYTHON_WORKERS</code> - The number of workers to spawn for packages like gunicorn.
Default: <code>number of CPUs * 2 + 1</code>


### Static files

Static files will be served from the <code>public/</code> directory. These files will be served directly by Nginx.


### Custom nginx.conf

Like the standalone Nginx cartridge, its possible to provide your own server configuration to be included in the main <code>nginx.conf</code> file. A sample is provided in the cloned repo as <code>nginx.conf.erb.sample</code>. Simply remove the .sample suffix and commit the changes.<code>nginx.conf.erb</code> will be processed and included in the main configuration every time the server starts.
