# High-level Approach
The project handle the core functionalities as follows:
1. Update: The router adds a new route to the forwarding table if it does not already exist. It then forwards the update message to eligible neighbors, following BGP propagation rules.
2. Withdraw: The router removes the specified route from the forwarding table by rebuilding the table based on the message history. After updating the table, it forwards the withdrawal message to the appropriate neighbors.
3. Data forwarding: Data packets are forwarded based on longest prefix match and the five selection rules to determine the path
4. Dump: The router returns the current state of the forwarding table in response to a dump request
5. Aggregation: The router triggers aggregation when two or more adjacent routes with same attributes (AS Path, local preference, and origin) can be merged into a broader network prefix. When routes are withdrawn, the router can disaggregate previously aggregated routes. It rebuilds the forwarding table by referencing the message history and re-applies the aggregation logic to maintain optimal table size.
-------------------------------------------
# Challenges
## aggregation part
The most challenging part of the implementation was the route aggregation. The core challenges in this part are as following:
1. sort the route list: Since only routes with the same attributes can be merged, the routes are first grouped by their attributes (excluding the netmask). This is because when two routes are merged, the resulting netmask changes, allowing the new route to potentially merge with a third route.
After grouping, the routes within each group are sorted by their network addresses to ensure efficient comparison during the merging process.
2. determine if the two routes are adjacent: The main logic here is calculating the prefix length and slicing the prefix to verify that the binary difference between the two networks is exactly 1.
Additionally, it should ensure that the host part of the IP addresses is composed entirely of 0s, confirming that the addresses represent network addresses rather than individual hosts.
3. merge and iteration: The core logic here is similar to the problem of merging two sorted lists. Starting with the smallest elements, a pairwise merging is performed.
-------------------------------------------
# Overview of Test
## test commands
/run configs/<configs-file>
## strategy
The tests steps are following the 6 levels:
1. Basic message handling: handle update, data and dump messages. 
2. 5 BGP path selection rules for route priority
3. Handle message of withdraw and no route
4. Implementation of propagation policies
5. Forward the data based on the longest prefix
6. Route aggregation and disaggregation
## challenges & solution
1. withdraw not removing routes properly
Upon receiving a withdrawal message, the router failed to remove the corresponding routes from the forwarding table
Used a temporary variable to store routes during withdrawal processing, but the forwarding table wasn’t directly updated.(Mistakenly set only the temp variable is not empty, the forwarding table should be changed)
Solutions: remove the "not empty condition"
2. incorrect route aggregation
During aggregation, only the first two adjacent networks were merged (.1.0 and .2.0), and the next two (.3.0 and .4.0) were merged separately.The aggregation logic did not re-iterate over merged routes to detect new merge opportunities.
Solutions: introduce the merge flag and modify to two loops