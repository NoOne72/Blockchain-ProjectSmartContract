# Blockchain-ProjectSmartContract
```
SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract AttendanceSystem {
    address public owner;
    
    uint public hadirCount;
    uint public alphaCount;
    uint public izinCount;
    
    // 1. ADDED: A variable to track the current attendance day/session
    uint public currentSessionId = 1; 
    
    // 2. CHANGED: Maps an address to the last Session ID they attended, instead of a true/false boolean
    mapping(address => uint) public lastRecordedSession;

    // Updated event to include the session ID
    event AttendanceRecorded(address student, string status, uint sessionId);

    constructor() {
        owner = msg.sender;
    }

    function markHadir() public {
        // 3. CHANGED: Check if their last recorded session is the current one
        require(lastRecordedSession[msg.sender] != currentSessionId, "You already recorded attendance for this session");
        
        lastRecordedSession[msg.sender] = currentSessionId; // Mark them present for the current session
        hadirCount += 1;
        emit AttendanceRecorded(msg.sender, "Hadir", currentSessionId);
    }

    function markAlpha() public {
        require(lastRecordedSession[msg.sender] != currentSessionId, "You already recorded attendance for this session");
        lastRecordedSession[msg.sender] = currentSessionId;
        alphaCount += 1;
        emit AttendanceRecorded(msg.sender, "Alpha", currentSessionId);
    }

    function markIzin() public {
        require(lastRecordedSession[msg.sender] != currentSessionId, "You already recorded attendance for this session");
        lastRecordedSession[msg.sender] = currentSessionId;
        izinCount += 1;
        emit AttendanceRecorded(msg.sender, "Izin", currentSessionId);
    }

    function getTotalAttendance() public view returns (uint totalHadir, uint totalAlpha, uint totalIzin, uint grandTotal) {
        uint total = hadirCount + alphaCount + izinCount;
        return (hadirCount, alphaCount, izinCount, total);
    }

    function resetAttendance() public {
        require(msg.sender == owner, "Only owner can reset attendance");
        hadirCount = 0;
        alphaCount = 0;
        izinCount = 0;
        
        // 4. THE MAGIC FIX: Move to the next session. 
        // Now, everyone's lastRecordedSession will be outdated, allowing them to record again!
        currentSessionId += 1; 
    }
}
