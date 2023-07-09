
# Ansible Role - potos\_dconf

This role works for me, but does not satisfy the potos acceptance rules yet:

* the automated checks are currently failing
* meta is not up to date

`dconf` is the [official way](https://help.gnome.org/admin/system-admin-guide/stable/dconf.html.en) to manage
gnome user and system settings and override gnome or distribution specific settings.

## Lock Down not supported (yet)

By default, gnome interprets system settings as defaults that can be overriden by the user.
But it is also possible to [lock down](https://help.gnome.org/admin/system-admin-guide/stable/dconf-lockdown.html.en)
settings, so users cannot change them any more.

[![Test](https://github.com/nis65/ansible-role-potos_dconf/actions/workflows/test.yml/badge.svg)](https://github.com/nis65/ansible-role-potos_dconf/actions/workflows/test.yml)

## Development hints

Be aware that the following commands can return different values
depending on the user session you are logged in right now.


* `dconf dump /` lists all dconf settings. This lists only
  the settings explicitly set somewhere.
* `gsettings list-recursively` lists really all variables
  currently known and their values. Use this to search for
  a certain setting, translate it back to `dconf` format
  and then implement it here.

## Example Playbook

As this role is tested via Molecule one can use [that
playbook](./molecule/default/converge.yml) as a starting point:

```yaml
---

- name: Converge
  hosts: all
  gather_facts: yes
  tasks:
    - name: run role
      ansible.builtin.include_role:
        name: 'ansible-role-potos_dconf'
```

## Role Variables

There are two ways to use this role to configure system wide dconf default settings.

* by setting `potos_dconf_20_advanced_wm_settings` to true. This activates a preconfigured
  set of `dconf` options. Look at the file `20_advanced_wm_settings.j2` to see what
  is actually configured. Most likely you don't like my very personal settings. In this
  case, you could expand this role an adding another template/variable.

* use the `potos_dconf_custom` list variable to configure arbitrary settings. This
  can be used as starting point for implementing your own preconfigured set.

You can use both ways in the same config, but: **WARNING** When the same `dconf` setting
is set multiple times, it is currently undefined what entry will win.

### potos\_dconf\_custom

Each element of the `potos_dconf_custom` variable must contain

* a `dconf_path` value
* a `settings` list that contains one or more dicts for the settings in that path, each dict containing
* the `name` of the setting
* the typed value of the setting, i.e. exactly one of
  * `value_bool`: true or false
  * `value_uint32`: an positive nteger
  * `value_string`: a string
  * `value_list_of_strings`: a list of strings

This can look like this:

```yaml
---

potos_dconf_custom:
  - dconf_path: "org/gnome/desktop/interface"
    settings:
      - name: "clock-show-seconds"
        value_bool: true
  - dconf_path: "org/gnome/desktop/session"
    settings:
      - name: "idle-delay"
        value_uint32: "120"
  - dconf_path: "org/gnome/shell"
    settings:
      - name: "favorite-apps"
        value_list_of_strings:
          - "amsel-menu.desktop"
          - "Local.desktop"
          - "Internet.desktop"
```

There is no checking on the values whatsoever. You have to
know what you do and test each setting carefully.

The default variables are defined in [defaults/main.yml](./defaults/main.yml):

```yaml
---

potos_dconf_20_advanced_wm_settings: false

potos_dconf_custom:
  - dconf_path: "org/gnome/shell"
    settings:
      - name: "favorite-apps"
        value_list_of_strings:
          - "org.gnome.Nautilus.desktop"
          - "libreoffice-writer.desktop"
          - "snap-store_ubuntu-software.desktop"
          - "yelp.desktop"
```

Another option is to use `ansible-doc` to read the argument specification:

```sh
ansible-doc --type role -r . main ansible-role-potos_dconf
```

## Requirements

N/A

## License

See [LICENSE](./LICENSE)

## Author Information

[Project Potos](https://github.com/projectpotos)

