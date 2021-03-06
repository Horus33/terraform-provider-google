---
# ----------------------------------------------------------------------------
#
#     ***     AUTO GENERATED CODE    ***    AUTO GENERATED CODE     ***
#
# ----------------------------------------------------------------------------
#
#     This file is automatically generated by Magic Modules and manual
#     changes will be clobbered when the file is regenerated.
#
#     Please read more about how to change this file in
#     .github/CONTRIBUTING.md.
#
# ----------------------------------------------------------------------------
layout: "google"
page_title: "Google: google_compute_url_map"
sidebar_current: "docs-google-compute-url-map"
description: |-
  UrlMaps are used to route requests to a backend service based on rules
  that you define for the host and path of an incoming URL.
---

# google\_compute\_url\_map

UrlMaps are used to route requests to a backend service based on rules
that you define for the host and path of an incoming URL.



<div class = "oics-button" style="float: right; margin: 0 0 -15px">
  <a href="https://console.cloud.google.com/cloudshell/open?cloudshell_git_repo=https%3A%2F%2Fgithub.com%2Fterraform-google-modules%2Fdocs-examples.git&cloudshell_working_dir=url_map_basic&cloudshell_image=gcr.io%2Fgraphite-cloud-shell-images%2Fterraform%3Alatest&open_in_editor=main.tf&cloudshell_print=.%2Fmotd&cloudshell_tutorial=.%2Ftutorial.md" target="_blank">
    <img alt="Open in Cloud Shell" src="//gstatic.com/cloudssh/images/open-btn.svg" style="max-height: 44px; margin: 32px auto; max-width: 100%;">
  </a>
</div>
## Example Usage - Url Map Basic


```hcl
resource "google_compute_url_map" "urlmap" {
  name        = "urlmap"
  description = "a description"

  default_service = "${google_compute_backend_service.home.self_link}"

  host_rule {
    hosts        = ["mysite.com"]
    path_matcher = "allpaths"
  }

  path_matcher {
    name            = "allpaths"
    default_service = "${google_compute_backend_service.home.self_link}"

    path_rule {
      paths   = ["/home"]
      service = "${google_compute_backend_service.home.self_link}"
    }

    path_rule {
      paths   = ["/login"]
      service = "${google_compute_backend_service.login.self_link}"
    }

    path_rule {
      paths   = ["/static"]
      service = "${google_compute_backend_bucket.static.self_link}"
    }
  }

  test {
    service = "${google_compute_backend_service.home.self_link}"
    host    = "hi.com"
    path    = "/home"
  }
}

resource "google_compute_backend_service" "login" {
  name        = "login"
  port_name   = "http"
  protocol    = "HTTP"
  timeout_sec = 10

  health_checks = ["${google_compute_http_health_check.default.self_link}"]
}

resource "google_compute_backend_service" "home" {
  name        = "home"
  port_name   = "http"
  protocol    = "HTTP"
  timeout_sec = 10

  health_checks = ["${google_compute_http_health_check.default.self_link}"]
}

resource "google_compute_http_health_check" "default" {
  name               = "health-check"
  request_path       = "/"
  check_interval_sec = 1
  timeout_sec        = 1
}

resource "google_compute_backend_bucket" "static" {
  name        = "static-asset-backend-bucket"
  bucket_name = "${google_storage_bucket.static.name}"
  enable_cdn  = true
}

resource "google_storage_bucket" "static" {
  name     = "static-asset-bucket"
  location = "US"
}
```

## Argument Reference

The following arguments are supported:


* `default_service` -
  (Required)
  The backend service or backend bucket to use when none of the given rules match.

* `name` -
  (Required)
  Name of the resource. Provided by the client when the resource is
  created. The name must be 1-63 characters long, and comply with
  RFC1035. Specifically, the name must be 1-63 characters long and match
  the regular expression `[a-z]([-a-z0-9]*[a-z0-9])?` which means the
  first character must be a lowercase letter, and all following
  characters must be a dash, lowercase letter, or digit, except the last
  character, which cannot be a dash.


- - -


* `description` -
  (Optional)
  An optional description of this resource. Provide this property when
  you create the resource.

* `host_rule` -
  (Optional)
  The list of HostRules to use against the URL.  Structure is documented below.

* `path_matcher` -
  (Optional)
  The list of named PathMatchers to use against the URL.  Structure is documented below.

* `test` -
  (Optional)
  The list of expected URL mappings. Requests to update this UrlMap will
  succeed only if all of the test cases pass.  Structure is documented below.
* `project` - (Optional) The ID of the project in which the resource belongs.
    If it is not provided, the provider project is used.


The `host_rule` block supports:

* `description` -
  (Optional)
  An optional description of this HostRule. Provide this property
  when you create the resource.

* `hosts` -
  (Required)
  The list of host patterns to match. They must be valid
  hostnames, except * will match any string of ([a-z0-9-.]*). In
  that case, * must be the first character and must be followed in
  the pattern by either - or ..

* `path_matcher` -
  (Required)
  The name of the PathMatcher to use to match the path portion of
  the URL if the hostRule matches the URL's host portion.

The `path_matcher` block supports:

* `default_service` -
  (Required)
  The backend service or backend bucket to use when none of the given paths match.

* `description` -
  (Optional)
  An optional description of this resource.

* `name` -
  (Required)
  The name to which this PathMatcher is referred by the HostRule.

* `path_rule` -
  (Optional)
  The list of path rules.  Structure is documented below.


The `path_rule` block supports:

* `paths` -
  (Required)
  The list of path patterns to match. Each must start with /
  and the only place a * is allowed is at the end following
  a /. The string fed to the path matcher does not include
  any text after the first ? or #, and those chars are not
  allowed here.

* `service` -
  (Required)
  The backend service or backend bucket to use if any of the given paths match.

The `test` block supports:

* `description` -
  (Optional)
  Description of this test case.

* `host` -
  (Required)
  Host portion of the URL.

* `path` -
  (Required)
  Path portion of the URL.

* `service` -
  (Required)
  The backend service or backend bucket link that should be matched by this test.

## Attributes Reference

In addition to the arguments listed above, the following computed attributes are exported:


* `creation_timestamp` -
  Creation timestamp in RFC3339 text format.

* `map_id` -
  The unique identifier for the resource.

* `fingerprint` -
  Fingerprint of this resource. This field is used internally during
  updates of this resource.
* `self_link` - The URI of the created resource.


## Timeouts

This resource provides the following
[Timeouts](/docs/configuration/resources.html#timeouts) configuration options:

- `create` - Default is 4 minutes.
- `update` - Default is 4 minutes.
- `delete` - Default is 4 minutes.

## Import

UrlMap can be imported using any of these accepted formats:

```
$ terraform import google_compute_url_map.default projects/{{project}}/global/urlMaps/{{name}}
$ terraform import google_compute_url_map.default {{project}}/{{name}}
$ terraform import google_compute_url_map.default {{name}}
```
