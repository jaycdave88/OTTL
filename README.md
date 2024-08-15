# OTTL Functions and Use Cases

The OpenTelemetry Transformation Language (OTTL) is a powerful tool for manipulating telemetry data within the OpenTelemetry Collector, offering a range of functions and verbs to transform metrics, traces, and logs before they are sent to monitoring systems. This introduction explores OTTL's key features, common use cases, and provides examples of how it can be used to enhance observability data processing.

## OTTL Functions Reference Table
The OpenTelemetry Transformation Language (OTTL) provides a set of functions for manipulating telemetry data. 

Here's a table summarizing key OTTL functions, their purposes, and examples:

| Function | Purpose | Example |
|----------|---------|---------|
| `Concat` | Concatenates strings with a delimiter | `Concat(": ", attributes["http.method"], attributes["http.path"])` |
| `Int` | Converts a value to an integer | `Int(attributes["http.status_code"])` |
| `DeleteKey` | Removes a key from a map | `DeleteKey(attributes, "sensitive_data")` |
| `DeleteMatchingKeys` | Removes keys matching a pattern from a map | `DeleteMatchingKeys(attributes, "secret.*")` |
| `KeepKeys` | Keeps only specified keys in a map | `KeepKeys(attributes, ["http.method", "http.path"])` |
| `Limit` | Limits the number of entries in a map | `Limit(attributes, 10, ["important_key"])` |
| `ReplaceAllMatches` | Replaces all occurrences of a string | `ReplaceAllMatches(body, "password=\\w+", "password=****")` |
| `ReplaceAllPatterns` | Replaces all regex matches | `ReplaceAllPatterns(body, "\\b\\d{3}-\\d{2}-\\d{4}\\b", "[SSN REDACTED]")` |
| `ReplaceMatch` | Replaces the first occurrence of a string | `ReplaceMatch(body, "secret", "****")` |
| `ReplacePattern` | Replaces the first regex match | `ReplacePattern(body, "api_key=\\w+", "api_key=****")` |
| `Set` | Sets a value | `Set(attributes["new_field"], "new_value")` |
| `Split` | Splits a string into an array | `Split(body, ",")` |
| `TraceID` | Converts bytes to a trace ID string | `TraceID(trace_id)` |
| `SpanID` | Converts bytes to a span ID string | `SpanID(span_id)` |
| `TruncateAll` | Truncates all string values in a map | `TruncateAll(attributes, 100)` |

## Email Scrubbing Examples
Here are examples of using OTTL in the OpenTelemetry Collector's transform processor to handle sensitive information:

1. Email Scrubbing:
```
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Z|a-z]{2,}\\b", "[EMAIL REDACTED]")
```

2. Email Masking:
```
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(attributes["email"], "(?<=.{3}).(?=[^@]*?@)", "*")
```

3. Email Removal:
```
transform:
  log_statements:
    - context: log
      statements:
        - delete_key(attributes, "email")
```

## Cloud Provider Redaction Examples
Here are examples of using OTTL in the OpenTelemetry Collector's transform processor to handle sensitive information from Cloud Providers:

1. AWS Access Key ID Redaction:
```
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "(?i)(aws_access_key_id|access key|aws access)\\s*[:=]\\s*[A-Z0-9]{20}", "$1: [REDACTED]")
```

2. AWS Secret Access Key Redaction:
```
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "(?i)(aws_secret_access_key|secret access key|secret key)\\s*[:=]\\s*[A-Za-z0-9/+=]{40}", "$1: [REDACTED]")
```

3. Azure SQL Connection String Redaction:
```
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "(?i)(Server|Database|User ID|Password)=[^;]+", "$1=[REDACTED]")
```

4. Google API Key Redaction:
```
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "(?i)(g_places_key|gcp api key|google-api-key|x-goog-api-key)\\s*[:=]\\s*[A-Za-z0-9-_]{39}", "$1: [REDACTED]")
```

## Credit Card Redaction Examples

Here are examples of using OTTL in the OpenTelemetry Collector's transform processor to handle sensitive credit card information:

1. American Express Card (15 digits):
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b3[47][0-9]{13}\\b", "[AMEX REDACTED]")
```

2. Diners Club Card (14 digits):
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b3(?:0[0-5]|[68][0-9])[0-9]{11}\\b", "[DINERS REDACTED]")
```

3. Discover Card (16 digits):
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b6(?:011|5[0-9]{2})[0-9]{12}\\b", "[DISCOVER REDACTED]")
```

4. JCB Card (16 digits):
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b(?:2131|1800|35\\d{3})\\d{11}\\b", "[JCB REDACTED]")
```

5. Mastercard (16 digits):
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b5[1-5][0-9]{14}\\b", "[MASTERCARD REDACTED]")
```

6. Visa Card (13-16 digits):
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b4[0-9]{12}(?:[0-9]{3})?\\b", "[VISA REDACTED]")
```

7. IBAN (International Bank Account Number):
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b[A-Z]{2}[0-9]{2}[A-Z0-9]{11,30}\\b", "[IBAN REDACTED]")
```

## Personal Data Redaction Examples

Here are examples of using OTTL in the OpenTelemetry Collector's transform processor to handle various types of sensitive information:

1. Canadian Social Insurance Number:
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b\\d{3}-\\d{3}-\\d{3}\\b", "[SIN REDACTED]")
```

2. Chinese Car License Plate Number:
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b[京津沪渝冀豫云辽黑湘皖鲁新苏浙赣鄂桂甘晋蒙陕吉闽贵粤青藏川宁琼使领][A-Z][A-Z0-9]{5,6}\\b", "[LICENSE PLATE REDACTED]")
```

3. Chinese Passport Number:
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b[GE]\\d{8}\\b", "[PASSPORT REDACTED]")
```

4. Chinese Phone Number:
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b(?:(?:\\+|00)86)?1[3-9]\\d{9}\\b", "[PHONE REDACTED]")
```

5. Chinese Vehicle Identification Number:
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b[A-HJ-NPR-Z0-9]{17}\\b", "[VIN REDACTED]")
```

6. France Social Security Number (INSEE/NIR):
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b[12]\\s?\\d{2}\\s?\\d{2}\\s?\\d{2}\\s?\\d{3}\\s?\\d{3}\\s?\\d{2}\\b", "[INSEE REDACTED]")
```

7. UK National Insurance Number:
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b[A-CEGHJ-PR-TW-Z]{1}[A-CEGHJ-NPR-TW-Z]{1}[0-9]{6}[A-D]\\b", "[NI NUMBER REDACTED]")
```

8. US Individual Taxpayer Identification Number (ITIN):
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b9\\d{2}-[7-9]\\d-\\d{4}\\b", "[ITIN REDACTED]")
```

9. US Social Security Number:
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b\\d{3}-\\d{2}-\\d{4}\\b", "[SSN REDACTED]")
```

## Network Data Redaction Examples

Here are examples of using OTTL in the OpenTelemetry Collector's transform processor to handle various types of sensitive network-related information:

1. HTTP Basic Authentication Header:
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(attributes["http.headers"], "(?i)Authorization:\\s*Basic\\s+[A-Za-z0-9+/=]+", "Authorization: Basic [REDACTED]")
```

2. HTTP Cookie:
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(attributes["http.headers"], "(?i)Cookie:\\s*([^;]+)", "Cookie: [REDACTED]")
```

3. HTTP(S) URL:
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "https?://[\\w-]+(\\.[\\w-]+)+([\\w.,@?^=%&:/~+#-]*[\\w@?^=%&/~+#-])?", "[URL REDACTED]")
```

4. IPv4 Address:
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b(?:\\d{1,3}\\.){3}\\d{1,3}\\b", "[IPv4 REDACTED]")
```

5. IPv6 Address:
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "(([0-9a-fA-F]{1,4}:){7,7}[0-9a-fA-F]{1,4}|([0-9a-fA-F]{1,4}:){1,7}:|([0-9a-fA-F]{1,4}:){1,6}:[0-9a-fA-F]{1,4}|([0-9a-fA-F]{1,4}:){1,5}(:[0-9a-fA-F]{1,4}){1,2}|([0-9a-fA-F]{1,4}:){1,4}(:[0-9a-fA-F]{1,4}){1,3}|([0-9a-fA-F]{1,4}:){1,3}(:[0-9a-fA-F]{1,4}){1,4}|([0-9a-fA-F]{1,4}:){1,2}(:[0-9a-fA-F]{1,4}){1,5}|[0-9a-fA-F]{1,4}:((:[0-9a-fA-F]{1,4}){1,6})|:((:[0-9a-fA-F]{1,4}){1,7}|:)|fe80:(:[0-9a-fA-F]{0,4}){0,4}%[0-9a-zA-Z]{1,}|::(ffff(:0{1,4}){0,1}:){0,1}((25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])\\.){3,3}(25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])|([0-9a-fA-F]{1,4}:){1,4}:((25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])\\.){3,3}(25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9]))", "[IPv6 REDACTED]")
```

6. MAC Address:
```yaml
transform:
  log_statements:
    - context: log
      statements:
        - replace_all_patterns(body, "\\b([0-9A-Fa-f]{2}[:-]){5}([0-9A-Fa-f]{2})\\b", "[MAC REDACTED]")
```

