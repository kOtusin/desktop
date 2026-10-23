--- a/ipc/chromium/src/base/process_util_mac.mm
+++ b/ipc/chromium/src/base/process_util_mac.mm
@@ -85,11 +85,20 @@
   }
 
   if (!options.workdir.empty()) {
-    int rv = posix_spawn_file_actions_addchdir_np(&file_actions,
-                                                  options.workdir.c_str());
-    if (rv != 0) {
-      DLOG(WARNING) << "posix_spawn_file_actions_addchdir_np failed";
-      return Err(LaunchError("posix_spawn_file_actions_addchdir", rv));
+    // zen-macos-legacy: posix_spawn_file_actions_addchdir_np is 10.15+.
+    // On macOS the gecko launch paths never set a workdir (only the Linux
+    // fork server does), so on older systems return a clean error instead
+    // of silently launching in the wrong directory.
+    if (__builtin_available(macOS 10.15, *)) {
+      int rv = posix_spawn_file_actions_addchdir_np(&file_actions,
+                                                    options.workdir.c_str());
+      if (rv != 0) {
+        DLOG(WARNING) << "posix_spawn_file_actions_addchdir_np failed";
+        return Err(LaunchError("posix_spawn_file_actions_addchdir", rv));
+      }
+    } else {
+      DLOG(WARNING) << "workdir unsupported on macOS < 10.15";
+      return Err(LaunchError("posix_spawn_file_actions_addchdir", ENOTSUP));
     }
   }
 
