<details>
  <summary>AWS S3 - Simple storage services</summary>
  <details>
    <summary>Access control mechanisms</summary>

    1. IAM Roles and Policies 
    - Use IAM roles and IAM policies to grant the minimum permissions necessary for <b>users and applications</b>.
    - We should use this one when we are seeking access from other aws services like EC2 instance, Lambda, Glue
    2. Bucket Policies
    - Define explicit permissions using Bucket Policies. Ensure the policy grants access only to specific  <b> users, IP addresses, or AWS services. </b>
    - This policy we can apply while we are providing any public access to anonymous user, IP bound users or specific IP range or Cross aws Account user. 
    - For sensitive data, explicitly deny public access and restrict access to specific AWS accounts or VPCs.
    - Sample Bucket Policy :
      ```JSON
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::bucket-name/*",
            "Condition": {
              "StringNotEquals": {
                "s3:x-amz-server-side-encryption": "aws:kms"
              }
            }
          }
        ]
      }
      ```
    3. Access control list
    - If using ACLs, ensure that they only grant permissions to specific AWS accounts or users. Avoid using ACLs that provide public or overly permissive access.
    - We can give access using Canonical user ID. Which is reside in security credential -> Account details Tab

  </details>

  <details>
    <summary>Encryptions</summary>

    - This is the first item inside Section 2.
    - Add more bullet points as needed.
    - Example code block:
    
      ```python
      print("Section 2 code block")
      ```

  </details>

  <details>
    <summary>VPC endpoint for private connectivity</summary>

    - This is the first item inside Section 3.
    - You can include text, images, or even more collapsible sections.
    - Example code block:
    
      ```python
      print("Section 3 code block")
      ```

  </details>
  <details>
    <summary>Block public access</summary>

    - This is the first item inside Section 3.
    - You can include text, images, or even more collapsible sections.
    - Example code block:
    
      ```python
      print("Section 3 code block")
      ```

  </details>
  <details>
    <summary>MFA Deletes</summary>

    - This is the first item inside Section 3.
    - You can include text, images, or even more collapsible sections.
    - Example code block:
    
      ```python
      print("Section 3 code block")
      ```

  </details>
  <details>
    <summary>Pre-signed URL</summary>

    - This is the first item inside Section 3.
    - You can include text, images, or even more collapsible sections.
    - Example code block:
    
      ```python
      print("Section 3 code block")
      ```

  </details>
  <details>
    <summary>Bucket Versioning</summary>

    - This is the first item inside Section 3.
    - You can include text, images, or even more collapsible sections.
    - Example code block:
    
      ```python
      print("Section 3 code block")
      ```

  </details>
  <details>
    <summary>Object Lock</summary>

    - This is the first item inside Section 3.
    - You can include text, images, or even more collapsible sections.
    - Example code block:
    
      ```python
      print("Section 3 code block")
      ```

  </details>
  <details>
    <summary>Monitor Access and Activity Logging</summary>

    - This is the first item inside Section 3.
    - You can include text, images, or even more collapsible sections.
    - Example code block:
    
      ```python
      print("Section 3 code block")
      ```

  </details>

</details>
