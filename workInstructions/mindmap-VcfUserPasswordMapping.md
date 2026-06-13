# Password management in VCS

# List of Changes
  
| Version | Date       | Description              | Author       |
| ------- | ---------- | ------------------------ | --------------- |
| 0.1     | 08/04/2021 | Draft version | Maciej Losek |

```mermaid
graph LR
    B(resetSddcManagerUsersPasswords.yml)---|include_role|B1>dhc-resetSddcManagerUsersPasswords]-->BA[Items]
    B---|include_role|BB>dhc-hardenBilling]
    style B fill:#00FF00,stroke-width:4px
    style B1 fill:#ADD8E6
    style BB fill:#ADD8E6
        BA-->BA1['admin', sdm001]
        BA-->BA2['vcf', sdm001]
        BA-->BA3['root', sdm001]
        BB.-|1. when item defined|BA2
        BB.->|2.|BA21(-update vCenter password in billing configuration-)
```
