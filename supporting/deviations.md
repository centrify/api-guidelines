# Deviations of note from Microsoft API Guidelines

While you can view version control to see the deviations, this is a useful summary of where we deviate from Microsoft's guidelines.

### 7.4. Supported methods

We strongly discourage the use of PUT. It has numerous issues around concurrency, clients which improperly handle new properties, or are erroneously using a new version of the API they do not handle welll.
Instead, use PATCH.

#### 7.4.2. PATCH

We prefer PATCH over PUT, and specifically JSON merge-patch ([RFC 7396](https://tools.ietf.org/html/rfc7396))

### 7.5. Standard request headers

TO REVIEW

### 7.6. Standard response headers

TO REVIEW

#### 7.10.1. Clients-specified response format

We only encourage support of JSON at this time.

#### 7.10.2. Error condition responses

We utilize RFC7807 and provide guidance around implementing that RFC.

Microsoft indicated they also would have embraced this, if it were around when they were setting their standards: https://github.com/microsoft/api-guidelines/issues/206.

### 7.11. HTTP Status Codes

We do not encourage investment in supporting *all* HTTP status codes.

Guidance is provided on Error Codes that may be used as a subset.

### 9.8. Pagination

We encourage server driven paging only. Client driven paging has proven to be a performance bottleneck and constraint which had long term detrimental effects.

## 10. Delta queries

We do not support delta queries at this time.

## 12. Versioning

Under review / updating.

## 17. Naming guidelines

We have significant deviations here from Microsoft, worth reviewing in its entirety.

Still under review / updating.

## 19. API definition

API definitions are required for services.