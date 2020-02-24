# Cardano-Decentralised-Stake-Pool-Registry
Organize the information flow of the metadata of the pool (name, ticker, description, homepage, ...) from the chain to the end-user applications

This document is based on 

- the [Design specification for Delegation and incentives in Cardano](https://hydra.iohk.io/build/790053/download/1/delegation_design_spec.pdf) (section 4.x), 
- the Experiences made so far with the ITNv1 GitHub-based [incentivised stakepool registry](https://github.com/cardano-foundation/incentivized-testnet-stakepool-registry) 
- and a suggestion made by Emurgo/Yoroi regarding [stakepool curation and reputation](https://github.com/Emurgo/EmIPs/pull/9).





[TOC]



# The goal

Organize information flow of Pool’s metadata (Name, Ticker, description, homepage, ...) to end-user applications with

1. Possibly no censorship
2. Possibly no illegal content
3. Redundant and resilient alternative paths
4. Improved user experience (informative, time-saving, secure, …) 
5. Low latency for 1000 pools list views 
6. Motivation for good behaviour
7. Tackling bad actors 
8. …

The design specification talks about multiple independent metadata proxies, who collect the JSON metadata hosted from pool operators and serve it in a partially centralised way to wallet applications, delegation centres, explorer websites and other applications. If no metadata is provided a pool should not show up in delegation centres. 

Some community members fear this can result in centralised censorship and control, as well as social tensions and accusations.

Others fear illegal content (even if only for some world regions) can cause issues for the whole protocol and ecosystem. Question is who is the publisher/relayer, and who is responsible for illegal, fraudulent, misleading, trademark or copyright violating content.

**We should try to strive for both an uncensored and curated option for end-users, by preventing raisin pecking. A participant who has made a certain decision should have to live with the benefits as well as consequences.**

## How to achieve?

Wallet Delegation centres should give the choice to end users if they want 

- RAW pool data from the chain registration and unfiltered metadata  
  or
- Curated content from at least one or multiple third-party proxy services

### RAW Metadata

This is pretty simple and straightforward. Show all pools having a valid chain registration, with their self-declared and published. 

[^Must pools provide a verified (working) abuse contact address?]: 

This is completely open and uncensored. Pools post any link to a JSON file, wallets try to download and parse it (and blacklist the pool if the file is too big or unparsable) and then show the metadata from that file. . Pools must ensure they publish legal content - for whatever country. Users accept the risk of potentially seeing some “risky” information.Users choosing this option will see some automatically calculated performance and desirability metrics besides the metadata declared by the pool. End-Users must verify and evaluate pools themselves. Metadata retrieval and refresh for each node’s URL might be slow. 

![pool-registry_concept_RAW](C:\Users\MarkusGuflerLimitis\Documents\git\Cardano-Decentralised-Stake-Pool-Registry\images\pool-registry_concept_RAW.png)

### Curated Metadata

This is a service, provided by at least one and possibly multiple curators acting as intermediate proxy services. Having multiple such providers limit centralisation effects. 

The benefit for users is to have additional filters and categories. For example, pools consciously causing instabilities or ineffective protocol execution in favour of personal profits. The curators can also assign badges/tags, that can't be determined in a mathematical/automated way. For example support of social projects, offer useful free services for the community and the ecosystem. But also things like: announced trusted peer services are effectively and permanently up & running.

![pool-registry_concept_curated](C:\Users\MarkusGuflerLimitis\Documents\git\Cardano-Decentralised-Stake-Pool-Registry\images\pool-registry_concept_curated.png)

All metadata additions are linked to the unique, chain originating pool ID to avoid deliberately falsified duplicates.

Wallet developers can choose which curators they include in their applications, or let the users freely configure any curators URL. 

This is where consequences come into play: No one is stopping anyone from making the effort, build their own curator service and ask wallet creators or end-users to use it in their wallets. If they don't agree with existing curators they should be able to create an alternative. But they should not be able to act in a bad manner in the given free network infrastructure, just take the profits without experiencing any consequences.

This Curator concept - in an extreme form - would even allow having completely separated pool-lists for different target groups: one attracting convinced freethinkers, gamblers, etc... and another one for business or private people looking for a more reliable and protected environment.

Very probably most curators will try to list all pools and offer different target categories. Might some curator become lazy or offer high-quality curation only for a limited (reviewed) set of pools. 

# The general concept

Everyone should have a free choice: end-users choose whether they want to see raw or maintained data. The app developers can decide which curators they trust qualitatively and let end-users pick one from this pre-selection. Finally, the curators will need to define and communicate their criterias, what standards they use for their ratings and lists. Only good and permanently curated content will have success. 

A centralized gateway instance is avoided by the different possibilities and providers

- The pool metadata and curator flags data-structure is a defined standard, open for future enhancements (eg unicolor pool icon URL)

## The Pool operators point of view

- The basic source of metadata for a pool is always the pool operator himself. 
- He publishes the data on his own server/service
- He assumes full international legal responsibility
- His own checksum ensures that his data reaches the end user unchanged

## The curators point of view

- He regularly fetches new pool registrations from chain and accept them based on his own rules (eg keyword blacklists) 
- Too long contents or an invalid data format result in an exclusion
- He can add quality/reputation badges/flags or measured metrics in order to support “moral incentives” and make the pools comparable by such criterias
- He can add additional descriptions/reviews (eg explain why a certain flag)
- He provide an unique URL / Hostname everyone can use as endpoint
- He has to build his own brand and reputation, to become frequently used. 
- His own checksum ensures that his data reaches the end user unchanged

## The Developers point of view

- too long contents or an invalid data format result in an exclusion
- The basic refresh happens with Application side polling
- The application set if and how long the data is cached before a refresh lookup happens
- The application asks the user about his preferred data source, or offers a self-maintained preselection of curators.

## The End users point of view

- He can freely choose the applications including their options for pool metadata information flow
- He can freely choose from multiple options offered by the application
- he can and should find the right pool to delegate his stake. Happy end



# Technical considerations

it seems reasonable to define a universal metadata format. So not only from the point of view of the individual pool, but already in terms of lists of multiple pools, and with the extensions a curator can add. This uniform structure makes it much easier to handle the different access levels in a flexible way.



The wording used in the design specification implies a http/web based publication and communication system. This also sounds quite obvious and useful to solve the tasks. 

Nevertheless, another, DNS-based alternative should also be considered in this draft in order to better recognize the advantages and disadvantages of both solutions.

## WEB-Based

Use REST API webservices …



## DNS-based

Use DNS (Domain name system) an existing redundant and fast protocol, with many available implementations.

- A Pool operator has to register his own domain or use an existing one
- The metadata is published in DNS-TXT records
- The TXT record name contains the first 63 characters of the unique Pool ID. (DNS max hostname length = 63)
- The metadata is signed with the pool’s private key for quick and reliable authenticity verification
- DNSSEC is optional / required (?)
- The pool operator can update his own metadata at any time (or only with certificate re-registration?)
- Wallets/Applications/Proxies can query this information in a performant and lightweight way (DNS requests) 
- In analogy to DNS root-servers there can be curator’s as intermediate links between pool domains and applications
- Applications and Curators can use TTL settings for refresh requests and set their own min thresholds

## Open Questions

1. What if different curators assign different flags/opinions for same pool?
2. What if illegal content slips through? 
3. Is the content “hosted” by the pool operators DNS/Webserver?