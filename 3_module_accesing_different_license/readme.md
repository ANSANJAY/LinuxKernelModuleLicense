```bash
$make V=1
```

Setting verbose for make V=1 to make , to check all the logs. 

---
## Overview

This document provides an explanation of two Linux kernel modules, `module1.c` and `module2.c`, that are licensed under different licenses and have dependency relationships.

`module1.c` is a GPL-licensed module that exposes a function `myadd` which can add two integer numbers.

`module2.c` is a Proprietary-licensed module that uses the `myadd` function exposed by `module1.c`.

## Analysis

### Licensing

The GNU General Public License (GPL) requires that derivative works also be released under the GPL. When a GPL-licensed symbol like a function is exported (using `EXPORT_SYMBOL_GPL`), only other GPL-compatible modules can use it.

`module1.c` is correctly licensed under the GPL, and `module2.c` has a "Proprietary" license tag. Thus, `module2.c` is violating the GPL by linking against a GPL-only symbol (`myadd`), as it is not GPL-compatible.

### Expectation

When trying to load `module2`, you would expect to encounter a license mismatch error, and the kernel will refuse to load `module2` because it is proprietary and is trying to link to a GPL-only symbol. You would see error messages similar to below in the kernel log:

```shell
module2: disagrees about version of symbol myadd
module2: Unknown symbol myadd (err -22)
```

## Corrective Actions

To resolve this issue, there are a couple of approaches:

1. **Change the License of `module2.c` to GPL:**
   Changing the license of `module2.c` to GPL would resolve the licensing conflict. This would make `module2.c` compliant with the GPL license requirements.

   ```c
   MODULE_LICENSE("GPL");
   ```

2. **Use `EXPORT_SYMBOL`:**
   If the symbol in `module1.c` is exported using `EXPORT_SYMBOL` instead of `EXPORT_SYMBOL_GPL`, it would allow non-GPL modules like `module2.c` to link against it. However, this action might not comply with the intent behind releasing `module1.c` under GPL, as it would allow proprietary modules to link against it.

   ```c
   EXPORT_SYMBOL(myadd);
   ```

## Conclusion

In Linux Kernel Development, respecting the license of the modules and the symbols is crucial. 
- Violating GPL can lead to legal consequences and is against the open-source philosophy of contributing and sharing knowledge and code.
- Developers need to be mindful of the licenses of the code they are using and must make efforts to resolve any license discrepancies to contribute to a healthy open-source ecosystem.

