# Password management in VCS

# List of Changes
  
| Version | Date       | Description              | Author       |
| ------- | ---------- | ------------------------ | --------------- |
| 0.1     | 08/04/2021 | Draft version | Maciej Losek |

```mermaid
graph LR
    C(AD service accounts)-->CA["'svc-[locationCode]-vcs01'"]
        style C fill:#008000,stroke-width:4px
        CA-->CA11(-mgmt vCenter service account-)
            CA11-->CB111("wiPasswordResetOverview.md -> #vCenter service account")
    C-->CC["'svc-[locationCode]-nsx01'"]
        CC-->CC11(- nsx-t service account -)
            CC11-->CC111("wiPasswordResetOverview.md -> #NSX-T service account")
    C-->CE["'svc-[locationCode]-ans01'"]
        CE-->CE11(-connection wrapper for Windows WinRM-)
            CE11-->CE111(wiPasswordResetOverview.md -> # Service accounts without dependency of VCS components)
            style CE111 fill:#800080
    C-->CF["'svc-[locationCode]-ans02'"]
        CF-->CF11(-connection wrapper for Linux SSH -)
            CF11-->CE111
    C-->CG["'svc-[locationCode]-ans03'"]
        CG-->CG11(-manages entries in VCS password manager -)
            CG11-->CE111
    C-->CH["'svc-[locationCode]-aut01'"]
        CH-->CH11(-schedulers/cron jobs accounts-)
            CH11-->CH111('wiPasswordResetOverview.md -> # Cron service accounts')
    C-->CI["'svc-[locationCode]-aut02'"]
        CI-->CH11
    C-->CJ["'svc-[locationCode]-aut03'"]
        CJ-->CH11
    C-->CK["'svc-[locationCode]-bck01'"]
        CK-->CK11(-VCF external file-based backup user-)
            style CK11 fill:#FF0
    C-->CL["'svc-[locationCode]-git01'"]
        CL-->CL11(-GITLAB integration with Active Directory-)
            style CL11 fill:#FF0
    C-->CM["'svc-[locationCode]-idm01'"]
        CM-->CM11(-vIDM integration with Active Directory-)
            style CM11 fill:#FF0
    C-->CN["'svc-[locationCode]-kms01'"]
        CN-->CN11(-Key Management Service account -)
            CN11-->CN111(wiPasswordResetOverview.md -> # KMS service account)
    C-->CO["'svc-[locationCode]-lcm01'"]
        CO-->CO11(-Suite Administrator in vRSLCM -)
            CO11-->CE111
    C-->CP["'svc-[locationCode]-srm01'"]
    C-->CR["'svc-[locationCode]-vli01'"]
        CR-->CR11(-vLI->vSphere integration with VCS001-)
            style CR11 fill:#800000
            CR11-->CR111(wiPasswordResetOverview.md -> # VMware Log Insight service accounts)
              style CR111 fill:#800000
        CR-->CR12(-vLI->vSphere integration with VCS002-)
            style CR12 fill:#800000
            CR12-->CR111
    C-->CS["'svc-[locationCode]-vni01'"]
        CS-->CS1(-vNI->data source vcs001, vcs002-)
             style CS1 fill:#FF0
    C-->CT["'svc-[locationCode]-vcf01'"]
        CT-->CT11(-Service account for SDDC Manager password rotate-)
            CT11-->CE111
    C-->CU["'svc-[locationCode]-vlt01'"]
        CU-->CU11(-Service account for HashiVault-)
            CU11-->CE111
```
