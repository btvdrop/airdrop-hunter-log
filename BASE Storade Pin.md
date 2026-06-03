// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BASE_Storage_Pin_Claimer {
    address public owner;
    mapping(address => uint256) public storagePoints;
    mapping(address => uint256) public lastClaim;

    uint256 public constant DAILY_STORAGE_POINTS = 50;  // 毎日50ストレージポイント
    uint256 public constant COOLDOWN = 1 days;          // 24時間に1回

    event StorageClaimed(address indexed user, uint256 amount);

    constructor() {
        owner = msg.sender;
    }

    // 毎日ストレージポイントをclaim
    function claimStoragePoints() external {
        require(block.timestamp >= lastClaim[msg.sender] + COOLDOWN, "Too soon");
        lastClaim[msg.sender] = block.timestamp;
        storagePoints[msg.sender] += DAILY_STORAGE_POINTS;
        emit StorageClaimed(msg.sender, DAILY_STORAGE_POINTS);
    }

    // 自分のストレージポイント確認
    function getMyStoragePoints() external view returns (uint256) {
        return storagePoints[msg.sender];
    }

    // ownerだけが追加ポイント配布（テスト/イベント用）
    function addStoragePoints(address user, uint256 amount) external {
        require(msg.sender == owner, "Not owner");
        storagePoints[user] += amount;
        emit StorageClaimed(user, amount);
    }
}
