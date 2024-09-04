
# 🏛 Digital Will Management System

## 📄 Definition
This system allows users to create, manage, and execute digital wills. It stores wills, assets, beneficiaries, and executors securely using decentralized technology. The system ensures that wills are executed only by assigned executors and tracks assets and beneficiaries efficiently.

## 🚀 Features

### 👤 User Management
- 📝 **Create and manage** user profiles with personal details and email.
- ✏️ **Update** existing users with new details.
- 🔍 **View** users by ID or retrieve all users in the system.

### 🏛 Will Management
- 📝 **Create and manage** wills by assigning executors and adding assets and beneficiaries.
- ✏️ **Update** wills with new assets and beneficiaries.
- ❌ **Mark wills as executed** when the conditions of the will are met.
- 🔍 **View** wills by ID and retrieve all wills in the system.

### 🏗️ Asset Management
- 📝 **Add assets** to a will with descriptions and values.
- ✏️ **Update asset information** such as asset name and value.
- 🔍 **View all assets** associated with a specific will.

### 👥 Beneficiary Management
- 📝 **Add beneficiaries** to a will, specifying the share of assets.
- ✏️ **Update beneficiary information** such as the share percentage.
- 🔍 **View all beneficiaries** of a specific will.

### 🧑‍⚖️ Executor Management
- 📝 **Create executors** with contact details to manage wills.
- ✏️ **Update executor contact information** when needed.
- 🔍 **View executors** assigned to a specific will and retrieve all executors.

### 📝 Assign Executors
- 🔑 **Assign an executor** to a will ensuring that only authorized individuals can manage and execute the will.

## 💻 Sample Payloads

### User Payload
```json
{
  "name": "Jane Doe",
  "email": "janedoe@example.com"
}
```

### Will Payload
```json
{
  "userId": "user123",
  "executorId": "executor123"
}
```

### Asset Payload
```json
{
  "willId": "will123",
  "name": "Property",
  "value": 100000
}
```

### Beneficiary Payload
```json
{
  "willId": "will123",
  "name": "John Doe",
  "share": 50
}
```

### Executor Payload
```json
{
  "name": "Executor Name",
  "contact": "executor@example.com"
}
```



## Things to be explained in the course:
1. What is Ledger? More details here: https://internetcomputer.org/docs/current/developer-docs/integrations/ledger/
2. What is Internet Identity? More details here: https://internetcomputer.org/internet-identity
3. What is Principal, Identity, Address? https://internetcomputer.org/internet-identity | https://yumimarketplace.medium.com/whats-the-difference-between-principal-id-and-account-id-3c908afdc1f9
4. Canister-to-canister communication and how multi-canister development is done? https://medium.com/icp-league/explore-backend-multi-canister-development-on-ic-680064b06320

## How to deploy canisters implemented in the course

### Ledger canister
`./deploy-local-ledger.sh` - deploys a local Ledger canister. IC works differently when run locally so there is no default network token available and you have to deploy it yourself. Remember that it's not a token like ERC-20 in Ethereum, it's a native token for ICP, just deployed separately.
This canister is described in the `dfx.json`:
```
	"ledger_canister": {
  	"type": "custom",
  	"candid": "https://raw.githubusercontent.com/dfinity/ic/928caf66c35627efe407006230beee60ad38f090/rs/rosetta-api/icp_ledger/ledger.did",
  	"wasm": "https://download.dfinity.systems/ic/928caf66c35627efe407006230beee60ad38f090/canisters/ledger-canister.wasm.gz",
  	"remote": {
    	"id": {
      	"ic": "ryjl3-tyaaa-aaaaa-aaaba-cai"
    	}
  	}
	}
```
`remote.id.ic` - that is the principal of the Ledger canister and it will be available by this principal when you work with the ledger.

Also, in the scope of this script, a minter identity is created which can be used for minting tokens
for the testing purposes.
Additionally, the default identity is pre-populated with 1000_000_000_000 e8s which is equal to 10_000 * 10**8 ICP.
The decimals value for ICP is 10**8.

List identities:
`dfx identity list`

Switch to the minter identity:
`dfx identity use minter`

Transfer ICP:
`dfx ledger transfer <ADDRESS>  --memo 0 --icp 100 --fee 0`
where:
 - `--memo` is some correlation id that can be set to identify some particular transactions (we use that in the marketplace canister).
 - `--icp` is the transfer amount
 - `--fee` is the transaction fee. In this case it's 0 because we make this transfer as the minter idenity thus this transaction is of type MINT, not TRANSFER.
 - `<ADDRESS>` is the address of the recipient. To get the address from the principal, you can use the helper function from the marketplace canister - `getAddressFromPrincipal(principal: Principal)`, it can be called via the Candid UI.


### Internet identity canister

`dfx deploy internet_identity` - that is the canister that handles the authentication flow. Once it's deployed, the `js-agent` library will be talking to it to register identities. There is UI that acts as a wallet where you can select existing identities
or create a new one.

### Marketplace canister

`dfx deploy dfinity_js_backend` - deploys the marketplace canister where the business logic is implemented.
Basically, it implements functions like add, view, update, delete, and buy products + a set of helper functions.

Do not forget to run `dfx generate dfinity_js_backend` anytime you add/remove functions in the canister or when you change the signatures.
Otherwise, these changes won't be reflected in IDL's and won't work when called using the JS agent.

