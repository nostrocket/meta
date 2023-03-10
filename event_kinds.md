#### Event Kind 1031: new project creation Event   
- VALIDATE: `pubkey` MUST exist in rocketree


#### Event Kind 31031: project metadata update and witness (parametized replaceable event)   
- VALIDATE: `e` tag MUST point to a `Kind 1031` event
- VALIDATE: `pubkey` MUST be same as `Kind 1031` in `e` tag `||` exist in `votepower` array in most recent metadata.
- VALIDATE: `content` MUST be json
- VALIDATE `content.sequence` MUST be latest `sequence + 1` 
- VALIDATE `content.data` MUST follow rules defined for the parameter being set.

##### `content.data`
- current cap table
- last block height they saw (their client should auto-publish this whenever they see a block > current)
- Project Name
- Problem Statement TLDR
- Problem Statement Expanded
- Possible Solutions
- Project Description
- public Git repo for pull requests to be based on
- Possible revenue model ideas (what will people be paying for)
- Investment Prohibited? BOOL (can investors buy expense requests? If true, project will only be owned by contributors, and shares cannot be transferred to other pubkeys)
- Allowed Investor pubkeys (unrestricted if empty, project creator can add their own pubkey if they want to be the only one who can invest)
