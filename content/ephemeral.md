Title: Marking a Vertica Node as Ephemeral
Category: Tips
Date: 2014-11-13

First you will need to know the name of the node:

    select * from nodes;

Then mark it as ephemeral:

    alter node v_demo_node0001 is ephemeral;

