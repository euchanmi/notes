# RTB Auction Workflow

## Auction Workflow 

AuctionRunner ==> AuctionWorker ==> LocalBiddingActor ==> CampaignBiddingStrategy ==> CampaignBiddingStrategy ==> CampaignBiddingStrategy ==> InternalTacticAuctioneer ==> TacticBidder

## AuctionRunner   
* manages auction logic, notifications, logging and so on for single Auction instances  
* CampaignLoaderService calls AuctionRunner.updateBidder method to update bidders periodically  
	
###### RemoteBidderRegistry 
* This is for pub redirect stuff where we act like a AppNexus.  
* Baically we add our audience informaton,
  forward traffict to remote bidders specified in the bidder_endpoints,  
* refresh remote bidders every 10 minutes  
	   
## AuctionWorker

* AuctionRunner creates 256 ActorAuctionner with CyclicIterator load balancer actor.
* AuctionWorker essentially just sends BidRequest and AuctionRule to ActorAuctioneer
	   
## ActorAuctioneer 
* An auctioneer that communicates with bidders using message passing and thus 
  allows the bidders to have internal state that is not thread safe. It also passes the bid
  requests to all bidders in parallel and allows for individual bidders to time out
  without forfeiting the entire auction.
* It first checks a bidder (CampaignBiddingStrategy) to make sure if the bidder is applicable and if son, 
  sends bid request to the LocalBiddingActor associated with the bidder.
* ActorAuctioneer.updateBidder maps bidder and a LocalBiddingActor (create a new one if one already exists).

## LocalBiddingActor   
* On a receive of a bid request, it then calls bid fuction of its associated bidder 
  which is   CampaignBiddingStrategy
	
	
## CampaignBiddingStrategy
* CampaignBiddingStrategy < AbstractCampaignBiddingStrategy < ProxyBidder < Bidder with SegmentPacedBidding
* Basically it represents a campaign
* If cap(frequency cap, impression history) is not reached, it starts InternalTacticAuctioneer

## InternalTacticAuctioneer
* Basically it does an internal auction between tactic bidders (TacticBidder)

## TacticBidder
* TacticBidder extends CreativeSelectingBidder with SegmentPacedBidding with CampaignTacticBidder
* Represents a tactic
* This is the bidder(actually CreativeSelectingBidder) that actually bid (generests a Bid object)