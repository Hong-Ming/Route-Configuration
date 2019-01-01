# Route Configuration

This repository is a lab for NCTU course "Introduction to Computer Networks 2018".

---
## Abstract

In this lab, we are going to write a Python program with Ryu SDN framework to build a simple software-defined network and compare the different between two forwarding rules.

---
## Objectives

1. Learn how to build a simple software-defined networking with Ryu SDN framework
2. Learn how to add forwarding rule into each OpenFlow switch

---
## Execution

* How to run your program?

    First, we have to add some constrains to given network topology. Second, modify the code in controller.py to create a desire controller to this topology. To do this, we have to find out which port number at one switch or host connect to which port number at other switch or host. We can find this information in topo.py in the function self.addLink. For example, self.addLink(s1, h1, port1=1, port2=2) means that the port1 of s1 is connect to the port2 of h1. Now we can set the forwrding rule in each switch by modifing the parameter x in the right side of equal sign in "msg.datapath.id = x", "in_port = x", "ipv4_src = x", "ipv4_dst = x" and parameter x within the parenthesis in "actions = [parser.OFPActionOutput(x)]". The x in "msg.datapath.id = x" means the switch id, for example, x = 1 mean switch 1(s1). The x in "in_port = x" means input port. The x in "ipv4_src = x", "ipv4_dst = x" means source ip address and destination address respectively. The x in "actions = [parser.OFPActionOutput(x)]" mean the output port. For example, if I want to define a forward rule that if the pocket from h1 to h2 go in to s1 at port 1 and then this pocket will be forward to port 2 of s1. I have to set "msg.datapath.id = 1", "in_port = 1", "ipv4_src = 10.0.0.1", "ipv4_dst = 10.0.0.2", and "actions = [parser.OFPActionOutput(2)]".
* What is the meaning of the executing command (both Mininet and Ryu controller)?

    About the command "mn --custom topo.py --topo topo --link tc --controller remote". mn is to implement minimet --custom is to define custom topology topo.py, --topo is to set the topology, in this case we use topo, --link set the link parameter, in this case tc, --controller set the controller, in this case we set a remote controller. About the command "ryu-manager SimpleController.py --observe-links" ryu-manager is to implement ryu --observe-links is to observe link discovery event
* Show the screenshot of using iPerf command in Mininet (both `SimpleController.py` and `controller.py`)

 1. SimpleController.py
 
    ![alt text](https://github.com/nctucn/lab3-Hong-Ming/blob/master/Screen%20Shot%202018-12-31%20at%2011.13.41%20PM.png)
 2. controller.py
 
    ![alt text](https://github.com/nctucn/lab3-Hong-Ming/blob/master/Screen%20Shot%202018-12-31%20at%2011.19.13%20PM.png)

---
## Description

### Tasks

1. Environment Setup

    login to container, clone the git repository and check whether mininet command works.

2. Example of Ryu SDN

    login to container again, create two terminal window. Run SimpleTopo.py in one termainal then run SimpleController.py in another terminal to check whether it works.

3. Mininet Topology

    Copy the file SimpleTopo.py and rename it into topo.py and add the constrain in this new file. Finally, execute it to check whether it is correct.

4. Ryu Controller

    Copy the file SimpleController.py and rename it into controller.py and modify the code in the way I mention above.

5. Measurement

    Run topo.py with SimpleController.py and controller.py, measure the loss rate, bandwedth by iperf command.

### Discussion

> TODO:
> * Answer the following questions

1. Describe the difference between packet-in and packet-out in detail.

    Pocket-in is the event that a router get a pocket and redirect it to controller for further process. Pocket-out is the event that the controller send the pocket to desire destination port in router.
   
2. What is “table-miss” in SDN?

    When a pocket doesn't match everything in flow table then table-miss happen.
   
3. Why is "`(app_manager.RyuApp)`" adding after the declaration of class in `controller.py`?
   
    app_manager.RyuApp is the base class of ryu application, which will load all required ryu application model.
    
4. Explain the following code in `controller.py`.
    ```python
    @set_ev_cls(ofp_event.EventOFPPacketIn, MAIN_DISPATCHER)
    ```
    
    set_ev_cls is a decoder, the arguments in it tells when this decoder should be called. The first argumant indicate for which type of event occure should this docoder be called, in this case is ofp_event.EventOFPPacketIn which means whenever pocket in event occure, the decoder will be called. The second augument indicates the state of the switch, in this case is MAIN_DISPATCHER which means this decoder is called only after negotiation is completed.

5. What is the meaning of “datapath” in `controller.py`?

    Datapath is refer to switch in OpenFlow.
   
6. Why need to set "`ip_proto=17`" in the flow entry?

    set the ip protocol to 17
   
7. Compare the differences between the iPerf results of `SimpleController.py` and `controller.py` in detail.

    The iPerf result of Simplecontroller.py has larger loss rate then that of controller.py, it is because the routing path is different. The routing path from h2 to h1 of SimpleController.py going through h2 -> s3 -> s1 -> h1, which has the loss rate 3%, however, controller.py going through h2 -> s3 -> s2 -> s1 -> h1, which has the loss rate 1% + 1% = 2%.
   
8. Which forwarding rule is better? Why?

    controller.py is better. Because the loss rate from h2 to h1 is lower and also the routing path from h1 to h2 and h2 to h1 are different, which could lower the possibility of congestion. In SimpleController.py, the routing path from h1 to h2 and h2 to h1 are the same, which could cause the congestion occure more frequently. 

---
## References

* **Ryu SDN**
    * [Ryubook Documentation](https://osrg.github.io/ryu-book/en/html/)
    * [Ryubook [PDF]](https://osrg.github.io/ryu-book/en/Ryubook.pdf)
    * [Ryu 4.30 Documentation](https://github.com/mininet/mininet/wiki/Introduction-to-Mininet)
    * [Ryu Controller Tutorial](http://sdnhub.org/tutorials/ryu/)
    * [OpenFlow 1.3 Switch Specification](https://www.opennetworking.org/wp-content/uploads/2014/10/openflow-spec-v1.3.0.pdf)
    * [Ryubook 說明文件](https://osrg.github.io/ryu-book/zh_tw/html/)
    * [GitHub - Ryu Controller 教學專案](https://github.com/OSE-Lab/Learning-SDN/blob/master/Controller/Ryu/README.md)
    * [Ryu SDN 指南 – Pengfei Ni](https://feisky.gitbooks.io/sdn/sdn/ryu.html)
    * [OpenFlow 通訊協定](https://osrg.github.io/ryu-book/zh_tw/html/openflow_protocol.html)
* **Python**
    * [Python 2.7.15 Standard Library](https://docs.python.org/2/library/index.html)
    * [Python Tutorial - Tutorialspoint](https://www.tutorialspoint.com/python/)
* **Others**
    * [Cheat Sheet of Markdown Syntax](https://www.markdownguide.org/cheat-sheet)
    * [Vim Tutorial – Tutorialspoint](https://www.tutorialspoint.com/vim/index.htm)
    * [鳥哥的 Linux 私房菜 – 第九章、vim 程式編輯器](http://linux.vbird.org/linux_basic/0310vi.php)

---
## Contributors

* [Hong-Ming](https://github.com/Hong-Ming)
* [David Lu](https://github.com/yungshenglu)

---
## License

GNU GENERAL PUBLIC LICENSE Version 3
