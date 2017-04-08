Parametrized Factorio-init
==========================

This version of factorio-init allows you to have multiple environments
of factorio servers on the same machine. It also includes a Debian package
which will set up both the factorio server binaries and factorio-init.


Quickstart
----------

- Install this package.
- Run `factorio new-game somenameforyourgame`
- Run `factorio start` or `systemctl start factorio@default`
- Optionally: run on system boot: `systemctl enable factorio@default`
- To use another environment:
  - run `FENV=myenvname factorio new-game somenameforyourgame`
  - run `FENV=myenvname factorio start` or `systemctl start factorio@myenvname`


How does it work
----------------

Factorio-init uses the `FENV` environment variable. This defaults to `default`.
To explain what factorio-init does, here is an overview of what paths are used,
and what can be customized.


- Core game files are always installed to `/opt/factorio/factorio/`.

- Factorio-init helper files live in `/opt/factorio/factorio-init/`.

- The Debian package will create `/var/factorio/mods/`, in which you
  can store shared modpacks. More on this later.

- Every instance will have its write-data location set to 
  `/var/factorio/instance/{name}/`, where `{name}` defaults to `default`.
  This folder contains both `saves` and `mods` folders for this instance to use.
  The `server.out` and `server.pid` files will also be put here.
  Factorio-init automatically overwrites `config.ini` with the correct write-data entry.

- You can set the environment by setting `FENV` before using `factorio` command,
  which is the factorio-init script, not the actual factorio server binary.

- For auto-startup of factorio servers, you can use the parametrized systemd service.
  This allows you to enable/start a server like this: `systemctl start factorio@{name}`,
  where `{name}` is the name of the environment. There is no default, you will need to 
  manually run `systemctl start factorio@default` to enable the default server.


Configuration folder
--------------------

All configuration that factorio-init recognizes should live in `/etc/factorio`.
Factorio-init will load and try to inherit customizations in the correct order.
This however is not done for json files, those completely overwrite the defaults,
so ensure your customized server-settings.json contains all values.

Required configfiles:

- config.ini
- init.config
- server-settings.json

Optionally you may create `map-gen-settings.json` or one of the parametrized versions.

You can create overrides for specific instances as follows. 
In this example we create a instance with `FENV=asdf`.

- config.asdf.ini
- init.asdf.config
- server-settings.asdf.config
- map-gen-settings.asdf.config

Note that `init.asdf.config` will always inherit from `init.config`.

If you create `config.asdf.ini`, it will be copied to `/var/factorio/instances/config.ini`,
and the `write_dir` will always be overwritten.


Available values for init.config
--------------------------------

The following values all have sane defaults, but can be customized.

```
CMDOUT_FILENAME=factorio.log
DEBUG=0
EXTRA_BINARGS="--start-server-load-latest"  # you should use `factorio load-save` with this.
FACTORIO_PATH=/opt/factorio/factorio
RCON=""  # eg: --rcon-port=12345 --rcon-password=manysecure
SAVELOG=0
SERVER_IP=""
SERVER_PORT=34197
MOD_DIRECTORY=""  # eg: /var/factorio/mods/ArumbaMegaModPack
USERGROUP=factorio
USERNAME=factorio
WRITE_DIR="" # if this is set, it takes priority over WRITE_DIR_BASE
WRITE_DIR_BASE=/var/factorio/instances  # you should not change this.
```


Notable changes from upstream factorio-init
-------------------------------------------

The `install` and `update` commands have been removed, 
dependency on updater python script is also removed.


A note on dpkg's configfile handling
------------------------------------

Because the Debian package also creates /etc/server-settings.json and other default files,
`dpkg` will track those and notify when a package update tries to overwrite customized versions.

You can move the default files out of the way to prevent this message during updates:

`dpkg-divert --add --rename --divert /etc/factorio/.old-server-settings.json /etc/factorio/server-settings.json`

You can copy .old-server-settings.json or create a customized server-settings.json afterwards.
