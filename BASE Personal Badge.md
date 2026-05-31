// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BasePersonalBadge {
    string public name = "Base Personal Badge";
    string public symbol = "BPB";

    uint256 public totalSupply;
    mapping(address => uint256) public balanceOf;
    mapping(uint256 => address) public ownerOf;
    mapping(address => uint256) public lastMintDay;

    event BadgeMinted(address indexed to, uint256 tokenId, uint256 day);

    // 1日1回だけミント可能
    function mintBadge() external {
        uint256 today = block.timestamp / 1 days;
        require(lastMintDay[msg.sender] < today, "Already minted today");
        
        uint256 tokenId = totalSupply;
        totalSupply++;
        
        ownerOf[tokenId] = msg.sender;
        balanceOf[msg.sender]++;
        lastMintDay[msg.sender] = today;

        emit BadgeMinted(msg.sender, tokenId, today);
    }

    // 自分のバッジ数確認
    function getMyBadges() external view returns (uint256) {
        return balanceOf[msg.sender];
    }
}
