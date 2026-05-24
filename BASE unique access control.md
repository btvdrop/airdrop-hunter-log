// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BaseUniqueAccessControl {
    address public admin;

    constructor() {
        admin = msg.sender;
    }

    modifier onlyAdmin() {
        require(msg.sender == admin, "Not admin");
        _;
    }

    function changeAdmin(address newAdmin) public onlyAdmin {
        admin = newAdmin;
    }
}
