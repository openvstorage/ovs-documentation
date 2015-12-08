#### ALBA Config files
 The ALBA configuration file is located at `/opt/OpenvStorage/config/alba.json`.



```
{
    "nsm": {
        "safety": 3,
        "maxload": 75
    }
}
```

The different fields are:
* nsm:
    * safety: Amount of arakoon nodes in the NSM cluster. The value should be equal or lessthan the amount of master nodes. Default is 3.
    * maxload: Percentage when to create an additional NSM cluster during the NSM checkup. Default is 75% of 50 volumes.

**Note: Modifying/changing the config files incorrectly might result in data loss.**