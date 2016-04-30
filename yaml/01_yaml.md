!SLIDE bullets incremental
# Enter the YAML
* Natural starting point: trivially converted to/from JSON
* Easy syntax, quotes only where needed
* Commenting
* <http://yaml.org/>

~~~SECTION:notes~~~

Worry about a nice file first, but how do you pick a new file format?

~~~ENDSECTION~~~

!SLIDE bullets incremental
# ... buuuuut
* Nested arrays look ugly
* Lots of Packer config still needs quotes
* Direct conversion is still repetitious

~~~SECTION:notes~~~

Packer does use nested arrays, most notably for ordering postprocessors.

Remember when computers were going to save us from mundane repetitive tasks?

~~~ENDSECTION~~~

!SLIDE bullets incremental
# DRY it up
* (Don't Repeat Yourself)
* Include other YAML files
* Initial attempt at some top-level global settings

~~~SECTION:notes~~~

For simplicity, includes can't be nested.

Those global settings may go away. They don't do anything you can't do with
includes.

~~~ENDSECTION~~~

!SLIDE smaller
~~~FILE:centos.yml-00:yaml~~~

!SLIDE smaller transition=scrollUp
~~~FILE:centos.yml-01:yaml~~~

!SLIDE smaller transition=scrollUp
~~~FILE:centos.yml-02:yaml~~~

!SLIDE
# Builder Example
    @@@ yaml
    centos-6-virtualbox:
      config_template:
        - tmpl_vbox.yml
        - tmpl_vbox_iso.yml
      iso_url: iso/CentOS-6.7-x86_64-bin-DVD1.iso
      boot_command:
        - <tab> text ks=http://{{.HTTPIP}}:{{.HTTPPort}}/centos-6.ks<enter><wait>
      vm_name: "centos-6-{{user `stamp`}}"
      iso_checksum: c694ba4903ac2eb07d996ffa06b92d1dda78ec035d1ad87fdc9681e7245e7fc363eae987ec2476a408cf0bcaed0080ab05c7f26d7a9141eec8f898993c1057b1

~~~SECTION:notes~~~

Excerpt from the top-level YAML file.

~~~ENDSECTION~~~

!SLIDE small
    @@@ yaml
    output_directory: "builds/{{build_name}}-{{user `stamp`}}"
    ssh_password: vagrant
    ssh_wait_timeout: 10000s
    headless: "{{user `headless`}}"
    export_opts: 
      - --manifest
    ssh_username: vagrant
    vboxmanage: 
      - 
        - modifyvm
        - "{{.Name}}"
        - --memory
        - "1280"
      - 
        - modifyvm
        - "{{.Name}}"
        - --cpus
        - "1"
      - 
        - modifyvm
        - "{{.Name}}"
        - --vram
        - "12"
    shutdown_command: echo 'vagrant' | sudo -S /sbin/halt -h -p

~~~SECTION:notes~~~

tmpl\_vbox.yml, common to both ISO and OVF VirtualBox builders.

~~~ENDSECTION~~~

!SLIDE
    @@@ yaml
    # Overlay for tmpl_vbox.yml
    type: virtualbox-iso
    http_directory: http
    hard_drive_interface: sata
    guest_os_type: RedHat_64
    guest_additions_path: VBoxGuestAdditions_{{.Version}}.iso
    disk_size: 8192
    iso_checksum_type: sha512

~~~SECTION:notes~~~

tmpl\_vbox\_iso.yml, second template in the list.

~~~ENDSECTION~~~

!SLIDE
    @@@ yaml
    centos-6-virtualbox:
      config_template:
        - tmpl_vbox.yml
        - tmpl_vbox_iso.yml
      iso_url: iso/CentOS-6.7-x86_64-bin-DVD1.iso
      boot_command:
        - <tab> text ks=http://{{.HTTPIP}}:{{.HTTPPort}}/centos-6.ks<enter><wait>
      vm_name: "centos-6-{{user `stamp`}}"
      iso_checksum: c694ba4903ac2eb07d996ffa06b92d1dda78ec035d1ad87fdc9681e7245e7fc363eae987ec2476a408cf0bcaed0080ab05c7f26d7a9141eec8f898993c1057b1

~~~SECTION:notes~~~

Everything but config\_template is layered on top, and the name is moved in to
the "name" key.

Builders run in parallel so the order doesn't matter, so they can be named in
the YAML. Provisioners and postprocessors require a specific order, so they
would need a sort key.

~~~ENDSECTION~~~
