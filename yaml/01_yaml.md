!SLIDE bullets incremental
# Enter the YAML
* Natural starting point: trivially converted to/from JSON
* Easy syntax, quotes only where needed
* Commenting
* http://yaml.org/

!SLIDE bullets incremental
# ... buuuuut
* Nested arrays look ugly
* Lots of Packer config still needs quotes
* Direct conversion is still repetitious

!SLIDE bullets incremental
# DRY it up
* (Don't Repeat Yourself)
* Include other YAML files
* Initial attempt at some top-level global settings

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

