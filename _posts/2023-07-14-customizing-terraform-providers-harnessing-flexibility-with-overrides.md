---
title: "Customizing Terraform Providers: Harnessing Flexibility with Overrides"
categories: [terraform,provider]
tags: [golang,backend,terraform,provider]
---

# Introduction

In the realm of infrastructure as code (IaC), Terraform has emerged as a powerful tool for managing and provisioning resources across various cloud platforms. With its modular and declarative approach, Terraform provides a seamless experience for infrastructure deployment. However, as projects become more complex, developers and operators often encounter situations where they need to tailor their workflows to specific requirements. One such scenario arises when developing custom providers or configuring local interrupts for Terraform calls. In these instances, the need to override the default provider being used becomes paramount. Fortunately, Terraform offers a flexible solution in the form of provider overrides, allowing users to modify the source of the provider library using Terraformrc files. In this article, we delve into the intricacies of provider overrides and explore their significance in extending the capabilities of Terraform.

<hr/>

# All About Terraform rc file:
## Location:
On windows, the file should be placed in the `%APPDATA%` directory in a file named `.terraformrc`
On all other systems, the file must be named `.terraformrc` and be placed in the home directory of the pertaining user

# Sample File Content
```rc
provider_installation {
    dev_overrides {
        "registry.rishabh.com/provider/sample_provider" = "/path/where/provider/binary/is/stored/"
    }
}
```
Note: The path that you provide must be a directory in which the provider binary is stored. 
In the sample block, we've redefined provider source of "registry.rishabh.com/provider/sample_provider". 
Whenever we reference that provider source, terraform will use the compiled binary you have saved in the given directory