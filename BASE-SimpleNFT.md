// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BaseSimpleNFT {
    string public name = "Base Hunter NFT";
    string public symbol = "BHNFT";

    mapping(uint256 => address) public ownerOf;
    mapping(address => uint256) public balanceOf;
    uint256 public totalSupply;

    event Transfer(address indexed from, address indexed to, uint256 indexed tokenId);

    constructor() {}

    function mint(uint256 tokenId) public {
        require(ownerOf[tokenId] == address(0), "Token already minted");
        ownerOf[tokenId] = msg.sender;
        balanceOf[msg.sender]++;
        totalSupply++;
        emit Transfer(address(0), msg.sender, tokenId);
    }

    function transferFrom(address from, address to, uint256 tokenId) public {
        require(ownerOf[tokenId] == msg.sender || msg.sender == from, "Not owner");
        require(ownerOf[tokenId] != address(0), "Token not exist");
        ownerOf[tokenId] = to;
        balanceOf[from]--;
        balanceOf[to]++;
        emit Transfer(from, to, tokenId);
    }
}
