# azf-dnsworkaround01
Workaround for DNS private link resolution from on prem to the azure non prod and prod subscriptions.


Create a new zone with the privatelink as the zone name.
 - Give the zone an address of the private link.
 - Set a short TTL of 300 seconds for the zone in order to facilitate updates or back out.
 - create the zone on the powerdns internal servers net33100 and 33101. There is no need for external resoution as the address are private.

create the zone for the first private endpoint

```
pdnsutili create-zone stsaitnonprodfileshare01.file.core.windows.net
```

edit and add the records for net33101:

```

$ORIGIN .
stsaitnonprodfileshare01.files.core.microsoft.net      300   IN      SOA     ns22-3.sait.ab.ca postmaster.sait.ab.ca 2302280932 7200 3600 604800 300
stsaitnonprodfileshare01.files.core.microsoft.net      300   IN      A       10.88.96.4

```


Test to ensure that name resolution is working as expected.  if not back out the change as follows, do not proceed to add the prod DNS zone:

Backout plan:  delete the zone record on both servers

```

pdnsutili delete-zone stsaitnonprodfileshare01.file.core.windows.net

```

If name resolution works successfully for non_prod then create the zone for the prod subscription.


Create the zone for the first private endpoint on both servers

```

pdnsutili create-zone stsaitnonprodfileshare01.file.core.windows.net

```

Edit and add the records:

```

$ORIGIN .
stsaitprodfileshare01.file.core.windows.net       300   IN      SOA     ns22-3.sait.ab.ca postmaster.sait.ab.ca 2302280932 7200 3600 604800 300
stsaitprodfileshare01.file.core.windows.net       300   IN      A       10.88.32.5

```

Backout plan:  delete the zone record on both servers

```

pdnsutili delete-zone stsaitprodfileshare01.file.core.windows.net

```




