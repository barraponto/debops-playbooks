Playbook layout
===============

DebOps playbooks is split into several files to allow partial usage possible::

    site.yml
    |            ,---- <- root.yml
    |-- <- common.yml
    `-,
      |-- <- systems.yml
      |-- <- environments.yml
      |-- <- networking.yml
      |-- <- services.yml
      |-- <- applications.yml
      |-- <- virtualization.yml
      `-- <- hardware.yml

When you run ``debops`` script or ``ansible-playbook``, you can either run the
main ``site.yml`` playbook, or specify name of the playbook you want to use to
narrow the set of roles; this makes the Ansible runs shorter and lowers the
startup time.

Order of the playbooks apart from the common ones (``common.yml``,
``root.yml``) is not significant, although running roles that are used many
times as role dependencies first might make the whole playbook run faster.

List of playbooks
-----------------

Playbooks which are common for all hosts:

``site.yml``
  Main playbook, run by DebOps scripts by default, includes all other
  playbooks.

``common.yml``
  Playbook which runs on all hosts included in Ansible inventory. It executes
  a set of common roles which configure base services like SMTP service, a set
  of user accounts, ``ip(6)tables`` firewall, APT repositories, and so on.

``root.yml``
  This is a playbook required on all hosts that use DebOps roles, regardless if
  you are using the playbooks or not. It's included by the ``common.yml``
  playbook. It will set up custom Ansible facts required by some of the roles,
  like root paths for several directory types, host UUID, installation of
  scripts that generate facts on the fly, and so on.

  It also gathers the IP address of the Ansible Controller, or IP of the
  closest router which leads to it, to allow connections from that IP address
  through the firewall.

Playbooks which have only roles that are activated by specific Ansible host
groups:

``systems.yml``
  This playbook includes roles that configure services and resources that might
  be required by other roles, such as user and group accounts, authentication
  services like LDAP, network filesystems like NFS. Anything that is expected
  to be used by other roles further down the playbook, but it's not common
  enough to be included in the ``common.yml`` playbook, should be added here.

``environments.yml``
  This is a playbook focused on programming language environments, like Ruby,
  PHP, Java, NodeJS. Since these might be used by mutiple roles further down
  the playbook, they are grouped here to be run first so that other roles might
  be executed faster.

``networking.yml``
  Playbook whch focuses on roles that manage various network-related services,
  like DHCP, DNS, creating subnetworks or tunnels.

``services.yml``
  This playbook manages separate services like a webserver, various databases,
  file servers and others. These are usually standalone services which might be
  used by other roles down the line.

``applications.yml``
  This playbook manages either end-user applications which might use multiple
  services (usually web applications like GitLab or phpIPAM) or end-point
  applications which can be used by other hosts in the cluster, like iPXE, or
  rsnapshot.

``virtualization.yml``
  This playbook focuses on virtualization and hypervisors, like OpenVZ,
  KVM/libvirt or LXC.

``hardware.yml``
  At the end are roles which directly manage resources and services related to
  hardware, for example RAID health monitoring and notification.

