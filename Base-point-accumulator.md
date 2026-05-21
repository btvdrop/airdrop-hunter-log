// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BasePointAccumulator {
    // ポイント残高（アドレス → uint256）
    mapping(address => uint256) public points;

    // ポイント付与イベント（エクスプローラーでログが見やすい）
    event PointsEarned(address indexed user, uint256 amount, uint256 totalPoints);

    // デプロイヤー（owner）のみがポイント配布可能
    address public immutable owner;

    constructor() {
        owner = msg.sender;
    }

    // ポイントを付与（owner限定）
    function awardPoints(address user, uint256 amount) external {
        require(msg.sender == owner, "Only owner can award points");
        require(amount > 0, "Amount must be greater than 0");

        points[user] += amount;
        emit PointsEarned(user, amount, points[user]);
    }

    // 自分のポイントを確認（公開）
    function getMyPoints() external view returns (uint256) {
        return points[msg.sender];
    }

    // 任意アドレスのポイントを確認（公開）
    function getPointsOf(address user) external view returns (uint256) {
        return points[user];
    }
}
