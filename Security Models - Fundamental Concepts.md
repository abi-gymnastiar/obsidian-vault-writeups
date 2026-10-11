## Bell-LaPadula Model
Aims to achieve **confidentiality** by specifying three rules:
- **Simple Security Property:** Referred to as "no read up". It states that a subject at lower security level cannot read an object at a higher security level. This prevents access to sensitive information above the authorized level.
- **Star Security Property:** Referred to as "no write down". It states that a subject at a higher security level cannot write to an object at a lower security level. This prevents the disclosure of sensitive information to a subject of lower security level.
- **Discretionary-Security Property:** Uses an access matrix to allow read and write operations. Below is an example of an access matrix:

| Subjects  | Object A   | Object B  |
| --------- | ---------- | --------- |
| Subject 1 | Write      | No access |
| Subject 2 | Read/Write | Read      |
## Biba Model
Aims to achieve **Integrity** by specifying two main rules:
- **Simple Integrity Property:** Referred to as "no read down". A higher integrity subject should not read from lower integrity object.
- **Star Integrity Property:** Referred to as "no write up". A lower integrity subject should no write to a higher integrity object.
This model does not handle internal threats (insider threat).

## Clark-Wilson Model
Aims to achieve **integrity** by using the following concepts:
- **Constrained Data Item (CDI):** Refers to the data type whose integrity we want to preserve.
- **Unconstrained Data Item (UDI):** Refers to all data types beyond CDI, such as user and system input.
- **Transformation Procedures (TPs):** Programmed operations, such as read and write. Should maintain the integrity of CDIs.
- **Integrity Verification Procedures (IVPs):** Check and ensure the validity of CDIs.

## Other models
- Brewer and Nash model
- Goguen-Meseguer model
- Sutherland model
- Graham-Denning model
- Harrison-Ruzzo-Ullman model