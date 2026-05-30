// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BASE_Daily_Reward_Vault {
    address public owner;
    mapping(address => uint256) public lastClaim;
    uint256 public constant REWARD_AMOUNT = 0.0001 ether;  // 毎日0.0001 ETH
    uint256 public constant COOLDOWN = 1 days;             // 24時間に1回

    event Claimed(address indexed user, uint256 amount);

    constructor() {
        owner = msg.sender;
    }

    // 毎日報酬をclaim（クールダウンあり）
    function claimReward() external {
        require(block.timestamp >= lastClaim[msg.sender] + COOLDOWN, "Too soon");
        require(address(this).balance >= REWARD_AMOUNT, "Insufficient vault balance");

        lastClaim[msg.sender] = block.timestamp;
        payable(msg.sender).transfer(REWARD_AMOUNT);
        emit Claimed(msg.sender, REWARD_AMOUNT);
    }

    // コントラクトにETHを補充（ownerだけ）
    function fundVault() external payable onlyOwner {}

    // 残高確認（誰でも）
    function getVaultBalance() external view returns (uint256) {
        return address(this).balance;
    }

    // owner専用緊急引き出し
    function emergencyWithdraw() external onlyOwner {
        payable(owner).transfer(address(this).balance);
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        _;
    }
}
