# Voting.sol
Smart Contract (Solidity)
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Voting {
    address public owner;
    
    struct Poll {
        string question;
        mapping(uint => uint) votes;
        uint totalVotes;
        bool isOpen;
    }

    mapping(uint => Poll) public polls;
    uint public pollCount;

    event PollCreated(uint indexed pollId, string question);
    event Voted(uint indexed pollId, uint option, address voter);

    modifier onlyOwner() {
        require(msg.sender == owner, "Not the contract owner");
        _;
    }

    constructor() {
        owner = msg.sender;
    }

    function createPoll(string memory _question) external onlyOwner {
        pollCount++;
        polls[pollCount] = Poll(_question, 0, true);
        emit PollCreated(pollCount, _question);
    }

    function vote(uint _pollId, uint _option) external {
        require(polls[_pollId].isOpen, "Poll is closed");
        require(_option > 0 && _option <= 3, "Invalid option");

        polls[_pollId].votes[_option]++;
        polls[_pollId].totalVotes++;
        emit Voted(_pollId, _option, msg.sender);
    }

    function closePoll(uint _pollId) external onlyOwner {
        polls[_pollId].isOpen = false;
    }
}
