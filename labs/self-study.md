## Additional tasks

> [!NOTE]
> Topology and configuration are based on Lab10, 11 or 12.

### 1. Implement feature of disabling BGP sessions based on tags

Modify existing generators. If tag `damaged` is assigned to the device in Netbox Annet should administratively disable all BGP-sessions.  


**Result:**
```diff
  router bgp 65111
    address-family ipv4
+     neighbor 10.1.1.11 shutdown
+     neighbor 10.2.1.11 shutdown
```

### 2. Add static routes on ToR and announce it via BGP

1. Crate generator, which add static route on each ToR directed to `Null0`. Network is defined by **netbox device id** - `192.168.<netbox device id>.0/24`. **netbox device id** is attribute `id` of object `Device`.  
  New generator has to be added to result of function `get_generators()`, defined in file `lab_generators/__init__.py`.

2. Add route-map `IMPORT_STATIC` to the existing generators, which assign **bgp community** `65000:1` to all the routes

3. Add static routes redistribution via route-map `IMPORT_STATIC` to ToR's mesh

> [!NOTE]
> Mesh defines point-to-point addresses on connected interfaces, BGP peerings (`peers`) и BGP options (`global_options`) based on device connections in Netbox.
> - `global_options` is defined in function with decorator `device`,
> - `peers` is defined in function with decorator `direct`.  
> 
> You can find this functions in `mesh_view`. Redistribution parameters are in `global_options`.

**Result:**
```diff
# -------------------- tor-1-1.nh.com.cfg --------------------
+ ip route 192.168.7.0 255.255.255.0 Null0
+ route-map IMPORT_STATIC permit 10
+   set community 65000:1
  router bgp 65111
    address-family ipv4
+     redistribute static route-map IMPORT_STATIC
# -------------------- tor-1-2.nh.com.cfg --------------------
+ ip route 192.168.8.0 255.255.255.0 Null0
+ route-map IMPORT_STATIC permit 10
+   set community 65000:1
  router bgp 65112
    address-family ipv4
+     redistribute static route-map IMPORT_STATIC
# -------------------- tor-1-3.nh.com.cfg --------------------
+ ip route 192.168.9.0 255.255.255.0 Null0
+ route-map IMPORT_STATIC permit 10
+   set community 65000:1
  router bgp 65113
    address-family ipv4
+     redistribute static route-map IMPORT_STATIC
```
