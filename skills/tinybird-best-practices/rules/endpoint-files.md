# Endpoint Files

Endpoint files are `.pipe` files with `TYPE endpoint` and should live under `/endpoints`.

- Follow all general pipe rules.
- Ensure SQL follows Tinybird SQL rules (templating, SELECT-only, parameters).
- Include the output node in TYPE or in the last node.

Example:

```
DESCRIPTION >
    Some meaningful description of the endpoint

NODE endpoint_node
SQL >
    SELECT ...
TYPE endpoint
```
