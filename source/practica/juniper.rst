Juniper
=======

.. todo::

   - Se usa puerto de consola serial a ??? baudios

   - Para ayuda ir poniendo ``?``.

   login: root
   % cli
   > configure
   > set system root-authentication plain-text-password

Mostrar cosas::

  show configuration

  show interfaces
  show interfaces terse
  show interfaces detail

  show route
  show route terse
  show route detail

Habilitar o deshabilitar interfaz::

  configure
  set interface ge-0/0/1 enable
  set interface ge-0/0/1 disable

.. todo:: Agregar como borrar IP y como borrar rutas

Agregar IP::

  set interfaces ge-0/0/0 unit 0 family inet address 192.168.1.1/24
  delete interfaces ge-0/0/0 unit 0 family inet address 192.168.1.1/24

  set interfaces ge-0/0/0 unit 0 family inet6 address 2001:AA::2/64
  delete interfaces ge-0/0/0 unit 0 family inet6 address 2001:AA::2/64

Agregar rutas::

  set routing-options static route 0.0.0.0/0 next-hop 192.168.1.1
  delete routing-options static route 0.0.0.0/0 next-hop 192.168.1.1

  set routing-options rib inet6.0 static route ::/0 next-hop 2001:A::1/64
  delete routing-options rib inet6.0 static route ::/0 next-hop 2001:A::1/64

Configurar DNS::

  ip dns set servers=8.8.8.8

Dar IP a loopback::

  set interfaces lo0 unit 1 family inet address 192.168.1.1/24
  set interfaces lo0 unit 1 family inet6 address 2001:AA::2/64



.. todo::

  - https://rtodto.net/juniper-srx-for-beginners/

  - https://rtodto.net/srx-for-beginners-2/

  ::

    delete security
    set security forwarding-options family mpls mode packet-based
    set security forwarding-options family iso mode packet-based
    set security forwarding-options family inet6 mode packet-based

  ::

    root@JUNIPER_RACK_1# show
    ## Last changed: 2019-04-25 23:43:19 UTC
    version 11.2R4.3;
    system {
        root-authentication {
            encrypted-password "$1$UgrQvDCw$/KJ5/dy9ByGSLgUjh2viR1"; ## SECRET-DATA
        }
        name-server {
            208.67.222.222;
            208.67.220.220;
        }
        services {
            ssh;
            telnet;
            xnm-clear-text;
            web-management {
                http {
                    interface vlan.0;
                }
                https {
                    system-generated-certificate;
                    interface vlan.0;
                }
            }
            dhcp {
                router {
                    192.168.1.1;
                }
                pool 192.168.1.0/24 {
                    address-range low 192.168.1.2 high 192.168.1.254;
                }
                propagate-settings fe-0/0/0.0;
            }
        }
        syslog {
            archive size 100k files 3;
            user * {
                any emergency;
            }
            file messages {
                any critical;
                authorization info;
            }
            file interactive-commands {
                interactive-commands error;
            }
        }
        max-configurations-on-flash 5;
        max-configuration-rollbacks 5;
        license {
            autoupdate {
                url https://ae1.juniper.net/junos/key_retrieval;
            }
        }
    }
    interfaces {
        fe-0/0/0 {
            unit 0;
        }
        fe-0/0/1 {
            unit 0 {
                family ethernet-switching {
                    vlan {
                        members vlan-trust;
                    }
                }
            }
        }
        fe-0/0/2 {
            enable;
            unit 0 {
                family inet {
                    address 10.0.0.1/24;
                }
                family ethernet-switching {
                    vlan {
                        members vlan-trust;
                    }
                }
            }
        }
        fe-0/0/3 {
            unit 0 {
                family ethernet-switching {
                    vlan {
                        members vlan-trust;
                    }
                }
            }
        }
        fe-0/0/4 {
            unit 0 {
                family ethernet-switching {
                    vlan {
                        members vlan-trust;
                    }
                }
            }
        }
        fe-0/0/5 {
            unit 0 {
                family ethernet-switching {
                    vlan {
                        members vlan-trust;
                    }
                }
            }
        }
        fe-0/0/6 {
            unit 0 {
                family ethernet-switching {
                    vlan {
                        members vlan-trust;
                    }
                }
            }
        }
        fe-0/0/7 {
            unit 0 {
                family ethernet-switching {
                    vlan {
                        members vlan-trust;
                    }
                }
            }
        }
        vlan {
            unit 0 {
                family inet {
                    address 192.168.1.1/24;
                }
            }
        }
    }
    protocols {
        stp;
    }
    security {
        screen {
            ids-option untrust-screen {
                icmp {
                    ping-death;
                }
                ip {
                    source-route-option;
                    tear-drop;
                }
                tcp {
                    syn-flood {
                        alarm-threshold 1024;
                        attack-threshold 200;
                        source-threshold 1024;
                        destination-threshold 2048;
                        timeout 20;
                    }
                    land;
                }
            }
        }
        nat {
            source {
                rule-set trust-to-untrust {
                    from zone trust;
                    to zone untrust;
                    rule source-nat-rule {
                        match {
                            source-address 0.0.0.0/0;
                        }
                        then {
                            source-nat {
                                interface;
                            }
                        }
                    }
                }
            }
        }
        policies {
            from-zone trust to-zone untrust {
                policy trust-to-untrust {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
        zones {
            security-zone trust {
                host-inbound-traffic {
                    system-services {
                        all;
                    }
                    protocols {
                        all;
                    }
                }
                interfaces {
                    vlan.0;
                }
            }
            security-zone untrust {
                screen untrust-screen;
                interfaces {
                    fe-0/0/0.0 {
                        host-inbound-traffic {
                            system-services {
                                dhcp;
                                tftp;
                            }
                        }
                    }
                }
            }
        }
    }
    vlans {
        vlan-trust {
            vlan-id 3;
            l3-interface vlan.0;
        }
    }

         [edit]
    root@JUNIPER_RACK_1#
