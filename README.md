# Nano2
pragma solidity ^0.7.0;

import "https://github.com/OpenZeppelin/openzeppelin-solidity/contracts/math/SafeMath.sol";
import "https://github.com/OpenZeppelin/openzeppelin-solidity/contracts/token/ERC721/SafeERC721.sol";

// ERC-721 Token Name
string public constant NAME = "My ERC-721 Token";

// ERC-721 Token Symbol
string public constant SYMBOL = "MTOKEN";

// ERC-721 Token ID
uint256 public constant TOKEN_ID = 1;

// ERC-721 Token URI
string public constant TOKEN_URI = "https://mytokens.example.com/tokens/";

// ERC-721 Token metadata
string public metadata;

// ERC-721 Token Owner
address public owner;

// ERC-721 Token balance
mapping(address => uint256) public balanceOf;

// ERC-721 Token Ownership
mapping(uint256 => address) public tokenOwner;

// ERC-721 Token Approval
mapping(uint256 => address) public tokenApproval;

// ERC-721 Token Approval For All
mapping(address => mapping(address => bool)) public approvalForAll;

// ERC-721 Token Events
event Transfer(address from, address to, uint256 tokenId);
event Approval(address owner, address approved, uint256 tokenId);
event ApprovalForAll(address owner, address operator, bool approved);

// ERC-721 Token Constructor
constructor(string memory _metadata) public {
    owner = msg.sender;
    metadata = _metadata;
}

// ERC-721 Token Transfer Function
function transferFrom(address from, address to, uint256 tokenId) public {
    require(from != address(0), "Invalid address");
    require(to != address(0), "Invalid address");
    require(from != to, "Cannot transfer to self");
    require(tokenOwner[tokenId] == from, "Token is not owned by the sender");
    require(balanceOf[from] > 0, "Insufficient balance");
    require(approvalForAll[from][msg.sender] || tokenApproval[tokenId] == msg.sender, "Transfer not approved");
    balanceOf[from]--;
    balanceOf[to]++;
    tokenOwner[tokenId] = to;
    tokenApproval[tokenId] = address(0);
    emit Transfer(from, to, tokenId);
}

// ERC-721 Token Approval Function
function approve(address approved, uint256 tokenId) public {
    require(approved != address(0), "Invalid address");
    require(tokenOwner[tokenId] == msg.sender, "Token is not owned by the sender");
    tokenApproval[tokenId] = approved;
    emit Approval(msg.sender, approved, tokenId);
}

// ERC-721 Token Approval For All Function
function setApprovalForAll(
