
# ansible-grizzlaxy

Role for grizzlaxy applications.


## Variables

Set the following variables in the inventory:

* `app_name`: The name of the application.
* `app_port`: The port on which to serve the application.
* `app_host`: The hostname on which to serve the application.
* `app_repo`: Path to the application repository. See the requirements for the repository in the Repository section below.
* `app_tag`: Tag or branch of the app_repo to checkout.
* `app_user`: The user under which the app should be run and which owns the code and data.
* `app_enable_oauth`: Whether to enable OAuth (defaults to false, remember to override).
* `app_enable_ssl`: Whether to enable SSL (defaults to false, remember to override).
* `app_enable_sentry`: Whether to enable Sentry reporting (defaults to false, remember to override).
* `python_version`: Python version to use (should be >= 3.9).
* `conda_version`: Conda version to use. See the list [here](https://repo.anaconda.com/miniconda). Put the part of the name that's in between `Miniconda3` and `.sh`, e.g. `latest-Linux-aarch64` or `py311_23.10.0-1-Linux-x86_64`. Make sure it's the right architecture.
* `app_sentry_dsn`: Sentry parameter.
* `app_sentry_traces_sample_rate`: Sentry parameter.
* `app_sentry_environment`: Sentry parameter.


## Secrets

The following variables should be vault-encrypted:

* `app_ssl_cert`: SSL certificate.
* `app_ssl_key`: SSL private key.
* `google_client_id`: id for Google OAuth.
* `google_client_secret`: secret for Google OAuth.


## Optional variables

The defaults should work just fine, but you can change these if you want:

* `app_location`: The path on the target system in which to put all the code and data. Defaults to `/{{ app_name }}`
* `conda_base`: The path where to put miniconda (defaults to `{{ app_location }}/miniconda3`)


## Other variables

These variables you should not set, but they may be useful to define extra work to do in the playbook:

* `app_config`: Path to the configuration file.
* `app_code_path`: Path to the cloned repository.
* `conda_run`: Put that in front of a shell command to run it in the app's conda environment.


## Repository

The repository referenced by `app_repo` must follow certain rules in order to work with this role:

* The URL must be accessible from the host, evidently. It can be a path on the filesystem.
* The repository directory must be pip-installable using `pip -e {{ app_code_path }}`.
* Must contain a file named `pinned-requirements.txt` installable with `pip -r` which contains versions for all dependencies. You may generate this file using the `pip-compile` utility in the `pip-tools` package. Do not forget to commit it and update it whenever there are changes.
* Must contain a Python package named `{{ app_name }}`.
* The Python package must contain a `__main__` file.
  * The `__main__` script will be called like this via systemd: `python -m {{ app_name }} web --config {{ app_config }}`
