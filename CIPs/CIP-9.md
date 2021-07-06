|cip|title|author| discussions-to |status|type|created|
|:----:|:----|:----:|:----|:----|:----:|:----|
|9|General Certificate Contract Standard|ice0077<qhfice@126.com>，|<URL>|Draft|backward compatible|2020-9-26|

## Simple Summary

A standard template for a general certificate contract

## Abstract

This standard can be called by other contracts and is the basic function of general distribution. We consider the query and use of certificate owner and certificate issuer. The certificate owner owns the ownership of the digital certificate. Certificate issuers have the right to issue and interpret digital certificates.

The following standard specifies a key value format event log which include "ERC721", "Ownable", "RoleControl", "IERC721Mint" . Provide absolute and objective certificate deposit records for all kinds of users.Users (including other contracts) can call the contract interface to issue certificates.

## Motivation

* Since the main network of conflux blockchain was launched, there has been no standard for issuing authentication certificates on the chain. 
* With the training of conflux University College  coming to an end, in order to truly and effectively show the work of the college and the certification of students' learning achievements. Issue the certificate on the chain. At the same time, the certification certificate is issued as a standard, so as to ensure the standardization and effectiveness of other ecological certification certificates.
* Use blockchain smart contracts to reduce the rental and maintenance costs of certificate storage servers for certificate issuers; reduce annual fees paid to third-party electronic signature companies; avoid the risk of certificate data loss or damage due to natural or man-made reasons .
* The certificate issuing unit can significantly increase the credibility and consensus of the certificate.
* Users can view the certificate more conveniently and quickly and publicly; avoid the risk of certificate loss.
## Specification

```plain
// SPDX-License-Identifier: MIT
pragma solidity >=0.6.0 <0.8.0;
pragma experimental ABIEncoderV2;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "./RoleControl.sol";
import "./IERC721Mint.sol";

contract Certificate is ERC721, Ownable, RoleControl, IERC721Mint {
	///Call sub contract ERC721, ownable, rolecontrol, ERC721min
	///The address to create the calling contract is the contract owner
	///Creating NFT of erc721

    constructor () public ERC721(erc721Name, erc721Symbol) { 
    ///Certificate creation administrator "role" creates NFT initialization in erc721 forma

    modifier onlyMintAdmin() {
    ///Certificate creation administrator "role" account validity confirmation
     
    }

    function isMintAdmin(address account) public view returns (bool) {
    ///If the account number of the Certificate creation administrator "role" is confirmed to be valid, return "MINT"_ AMNIN_ ROLE"
    }

    function grantMintRole(address account) public onlyOwner {
    ///Set the current valid account to create an account for the certificate
    }

    function revokeMintRole(address account) public onlyOwner {
    ///Delete the account created by the certificate
    }

    function mint(address to, string memory tokenURI) public override onlyMintAdmin returns(uint256 tokenId) {
    ///Certificate code management
    }

    function transferFrom(address from, address to, uint256 tokenId) public virtual override {
    ///Certificate delivery mode 1: including "address from, address to, uint256 tokenid"        
    }
    function safeTransferFrom(address from, address to, uint256 tokenId) public virtual override {
    ///Certificate delivery mode 2: including "address from, address to, uint256 tokenid"

    }
    function safeTransferFrom(address from, address to, uint256 tokenId, bytes memory _data) public virtual override {
    ///Certificate delivery mode 3: including "address from, address to, uint256 tokenId, bytes memory _data"

    }

    function burn(uint256 tokenId) public override onlyMintAdmin {
    ///Confirm whether the token ID exists, and destroy it if it does not exist
    }

 }
```
* The certificate issuer uses the contract "Ownable.sol"
* The certificate owner uses the contract "RoleControl.sol"

Note: The standard specifies the format, and there are already specific implementation methods.

## Rationale

* TBD

## Backwards Compatibility

* TBD
## Example

Conflux University College Graduation Certificate

* [Certificate master contract](https://github.com/ice0077/CIP-9/blob/main/Certificate.sol)
```plain
...
contract Certificate is ERC721, Ownable, RoleControl, IERC721Mint {
    bytes32 private constant MINT_ADMIN_ROLE = keccak256("MINTER_ROLE");
    string private constant erc721Name = "College Certificate NFT";
    string private constant erc721Symbol = "CC-NFT";
    uint256 public tokenIdIndex;
    mapping(string => bool) tokenURIMap;
    constructor () public ERC721(erc721Name, erc721Symbol) { 
        _setupRole(MINT_ADMIN_ROLE, msg.sender);
    }
 ...

```

* [Certificate contract](https://github.com/ice0077/CIP-9/blob/main/ERC721.sol)
```plain
...
contract ERC721 is Context, ERC165, IERC721, IERC721Metadata, IERC721Enumerable {
    using SafeMath for uint256;
    using Address for address;
    using EnumerableSet for EnumerableSet.UintSet;
    using EnumerableMap for EnumerableMap.UintToAddressMap;
    using Strings for uint256;
    bytes4 private constant _ERC721_RECEIVED = 0x150b7a02;
    mapping (address => EnumerableSet.UintSet) private _holderTokens;
    EnumerableMap.UintToAddressMap private _tokenOwners;
    mapping (uint256 => address) private _tokenApprovals;
    mapping (address => mapping (address => bool)) private _operatorApprovals;
    string private _name;
    string private _symbol;
    mapping (uint256 => string) private _tokenURIs;
    string private _baseURI;
...
```
* [Certificate contract](https://github.com/ice0077/CIP-9/blob/main/IERC721Mint.sol)
```plain
// SPDX-License-Identifier: MIT
pragma solidity >=0.6.0 <0.8.0;
interface IERC721Mint {
    function mint(address to, string memory tokenURI) external returns(uint256 tokenId);
    function burn(uint256 tokenId) external;
    event MintEvent(address operator, address to, string tokenURI, uint256 tokenId);
}
```
* [Certificate creation](https://github.com/ice0077/CIP-9/blob/main/Ownable.sol)
```plain
// SPDX-License-Identifier: MIT
pragma solidity >=0.6.0 <0.8.0;
import "../GSN/Context.sol";
abstract contract Ownable is Context {
    address private _owner;
    event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);
    constructor () internal {
        address msgSender = _msgSender();
        _owner = msgSender;
        emit OwnershipTransferred(address(0), msgSender);
    }
    function owner() public view returns (address) {
        return _owner;
    }
    /**
     * @dev Throws if called by any account other than the owner.
     */
    modifier onlyOwner() {
        require(_owner == _msgSender(), "Ownable: caller is not the owner");
        _;
    }
    function renounceOwnership() public virtual onlyOwner {
        emit OwnershipTransferred(_owner, address(0));
        _owner = address(0);
    }
    function transferOwnership(address newOwner) public virtual onlyOwner {
        require(newOwner != address(0), "Ownable: new owner is the zero address");
        emit OwnershipTransferred(_owner, newOwner);
        _owner = newOwner;
    }
}
```
* [Certificate use](https://github.com/ice0077/CIP-9/blob/main/RoleControl.sol)
```plain
...
abstract contract RoleControl is Context {
    using EnumerableSet for EnumerableSet.AddressSet;
    using Address for address;
    struct RoleData {
        EnumerableSet.AddressSet members;
        bytes32 adminRole;
    }
    mapping (bytes32 => RoleData) private _roles;
    bytes32 public constant DEFAULT_ADMIN_ROLE = 0x00;
    event RoleAdminChanged(bytes32 indexed role, bytes32 indexed previousAdminRole, bytes32 indexed newAdminRole);
    event RoleGranted(bytes32 indexed role, address indexed account, address indexed sender);
    event RoleRevoked(bytes32 indexed role, address indexed account, address indexed sender);
    function hasRole(bytes32 role, address account) public view returns (bool) {
        return _roles[role].members.contains(account);
    }
    function getRoleMemberCount(bytes32 role) public view returns (uint256) {
        return _roles[role].members.length();
    }
    function getRoleMember(bytes32 role, uint256 index) public view returns (address) {
        return _roles[role].members.at(index);
    }
    function getRoleAdmin(bytes32 role) public view returns (bytes32) {
        return _roles[role].adminRole;
    }
    function renounceRole(bytes32 role, address account) public virtual {
        require(account == _msgSender(), "AccessControl: can only renounce roles for self");
        _revokeRole(role, account);
    }
    
    function _setupRole(bytes32 role, address account) internal virtual {
        _grantRole(role, account);
    }
    
    function _removeRole(bytes32 role, address account) internal virtual {
        _revokeRole(role, account);
    }
    
    function _setRoleAdmin(bytes32 role, bytes32 adminRole) internal virtual {
        emit RoleAdminChanged(role, _roles[role].adminRole, adminRole);
        _roles[role].adminRole = adminRole;
    }
    function _grantRole(bytes32 role, address account) private {
        if (_roles[role].members.add(account)) {
            emit RoleGranted(role, account, _msgSender());
        }
    }
    function _revokeRole(bytes32 role, address account) private {
        if (_roles[role].members.remove(account)) {
            emit RoleRevoked(role, account, _msgSender());
        }
    }
}
```

## Implementation

* Standard can be embedded in other contracts, also can be extended.
## Security Considerations

* TBD
## Copyright

* Copyright and related rights waived via[CC0](https://creativecommons.org/publicdomain/zero/1.0/?fileGuid=ioPgblI7nGIBMFSo).

