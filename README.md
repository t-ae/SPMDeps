# swift package resolve to wrong version

This project is made on macOS.

There's Linux-only dependency, so when `swift package resolve`, `Package.resolved` will be  dirty.

It seems that dependency causes other libraries' degration.

## Steps to reproduce

Execute the commands below on Linux

```
# git clone https://github.com/t-ae/SPMDeps.git
# cd SPMDeps
# git checkout 0.0.1
# swift package resolve
# git diff # Now Package.resolved is dirty
diff --git a/Package.resolved b/Package.resolved
index 388826c..e6c5e57 100644
--- a/Package.resolved
+++ b/Package.resolved
@@ -37,6 +37,15 @@
           "version": "1.9.200"
         }
       },
+      {
+        "package": "OpenSSL",
+        "repositoryURL": "https://github.com/Kitura/OpenSSL.git",
+        "state": {
+          "branch": null,
+          "revision": "80b04f33b086fc90e28d9ae159d43705fb348e16",
+          "version": "2.2.200"
+        }
+      },
       {
         "package": "SPMDepLib",
         "repositoryURL": "https://github.com/t-ae/SPMDepLib.git",

# git checkout -f 0.0.2
# swift package resolve
# git diff # Somehow SPMDepLib degrades
diff --git a/Package.resolved b/Package.resolved
index 0e4c54c..e6c5e57 100644
--- a/Package.resolved
+++ b/Package.resolved
@@ -37,13 +37,22 @@
           "version": "1.9.200"
         }
       },
+      {
+        "package": "OpenSSL",
+        "repositoryURL": "https://github.com/Kitura/OpenSSL.git",
+        "state": {
+          "branch": null,
+          "revision": "80b04f33b086fc90e28d9ae159d43705fb348e16",
+          "version": "2.2.200"
+        }
+      },
       {
         "package": "SPMDepLib",
         "repositoryURL": "https://github.com/t-ae/SPMDepLib.git",
         "state": {
           "branch": null,
-          "revision": "f92e3f0d0079e94a918333591efc11b16e7ec811",
-          "version": "0.0.2"
+          "revision": "6886f4618ef1e61449094c11b2302b1499038b20",
+          "version": "0.0.1"
         }
       },
       {
```

## Avoid degration

First `swift package resolve` writes the checkout information in `.build/workspace-state.json`.
Then second `swift package resolve` refers that json and resolves to wrong version.

To avoid this problem, run `swift package --force-resolved-versions resolve` before second `swift package resolve`.