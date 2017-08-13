---
layout: page
category: codex
section: Filter Hooks
title: ip-geo-block-upload-capability
file: [class-ip-geo-block.php]
---

Overwrite the `upload_files` capability.

<!--more-->

### Description ###

The capability [`upload_files`][UPLOAD-FILES] will be checked when you select 
"**Verify capability and MIME type**" as "**Prevent malicious file uploading**"
and a user requests to upload some files. But some of plugins or themes that 
support file uploading would tend to define their own capability, may be for 
the security reason.

In this case, you should overwrite the capability according to the plugin or 
theme you are employing.

### Parameters ###

- Bool $capability  
  Current user has the capability [`upload_files`][UPLOAD-FILES] or not.

### Use case ###

When a user has own role and capability (e.g. `attach_files`), overwrite the 
original one.

{% highlight ruby startinline %}
/**
 * Overwrite the `upload_files` capability.
 *
 * @param  Bool $capability `upload_files` capability of current user.
 * @return Bool TRUE if a user has right capability, FALSE if not.
 */
function my_upload_capability( $capability ) {
    if ( function_exists( 'wp_get_current_user' ) && ( $user = wp_get_current_user() ) ) {
        foreach ( $user->roles as $role ) {
            $role = get_role( $role );

            // if a user does not have `upload_files` but has `attach_files`
            if ( ! $capability && $role->has_cap( 'attach_files' ) ) {
                return TRUE;
            }
        }
    }

    return $capability;
}
add_filter( 'ip-geo-block-upload-capability', 'my_upload_capability' );
{% endhighlight %}

{% include alert-drop-in.html %}

### Since ###

3.0.4

### See also ###

- [3.0.4 Release Note][ReleaseNote304]
- [Roles and Capabilities][RoleCapability]
- [`get_allowed_mime_types()`][AllowedMIME]

[IP-Geo-Block]:   https://wordpress.org/plugins/ip-geo-block/ "WordPress › IP Geo Block « WordPress Plugins"
[UPLOAD-FILES]:   https://codex.wordpress.org/Roles_and_Capabilities#upload_files "Roles and Capabilities &laquo; WordPress Codex"
[RoleCapability]: https://codex.wordpress.org/Roles_and_Capabilities "Roles and Capabilities &laquo; WordPress Codex"
[AllowedMIME]:    https://developer.wordpress.org/reference/functions/get_allowed_mime_types/ "get_allowed_mime_types() | Function | WordPress Developer Resources"
[ReleaseNote304]: {{ '/changelog/release-3.0.4.html' | prepend: site.baseurl }} "3.0.4 Release Note | IP Geo Block"