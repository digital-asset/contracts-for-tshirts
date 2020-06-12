This is a repository for the [DAML](https://daml.com) smart contract code that'll power a (limited) ZuriHac T-Shirt giveaway. 
The idea is to make the giveaway a bit of fun competition. To participate in the , you'll need to write some [DAML](https://daml.com) contracts and run them using [Canton](https://canton.io) nodes. 

The rules of the competition will be determined by the smart contract run by a bot on a Canton node. Our current idea for the rules is as follows, though we are open to suggestions:
1. to participante in the contest, you simple create a participation request contract
2. the bot ranks the requests according to some criteria
3. at the set finishing time of the competition, no more requests are accepted, and the bot closes the competition and awards the prize to the winner(s)

The proposed criteria is that:
1. requests can be joint (i.e., have multiple signatories). This requires the submitters to cooperate, and design workflows to cooperate
2. we rank requests by the number of people on the request
