Download Link: https://assignmentchef.com/product/solved-coen146l-lab-6-stop-and-wait-for-an-unreliable-channel-with-loss
<br>
<h5>1.      To build a Stop and Wait reliable protocol on top of UDP to provide a reliable transport service while considering loss</h5>




<h5><strong>TFv3 – Stop and Wait for an Unreliable Channel, with Loss</strong></h5>

In Lab 5, you have developed the TFv2 which implements a reliable file transfer using Stop and Wait protocol rdt2.2. This version of file transfer is then TFv3.




TFv3 implements basically the protocol rdt3.0 presented in the textbook. It consists of a client and a server. Communication is unidirectional, i.e., data flows from the client to the server.




The server starts first and waits for messages. The client starts the communication. Messages have sequence or ack number 0 or 1 (start with zero). Before sending each message, a checksum is calculated and added to the header. After sending each message, the client starts a timer. Use select() for that. If select returns zero, there is no data, and the client needs to retransmit, restart the timer, and call select again. If select returns non-zero, there is data, so the client calls recvfrom to receive the ACK and then processes it. If ACK is not corrupted and the ack number is right, the client can now send one more message.




The server, after receiving a message, checks its checksum. If the message is correct and the seq number is right, the server sends an ACK message (according to the seq number) to the client, and the data is ready to be written in the file. Otherwise, the server repeats the last ACK message and waits to receive a message again. The server does not change much from the previous lab.




To verify your protocol, use the result of a random function to decide to send or skip a message, to decide to send or skip an ACK message (only change to the server), and to decide whether to send the right checksum or just zero. This will fake the error and loss effect.




<h5><strong>SELECT</strong></h5>

This is an example on how to use select (check the man page for includes):




// local variables needed

struct   timeval tv;         // timer

int        rv;                    // select returned value




// set it up, in the beginning of the function

fd_set   readfds;

fcntl (sock, F_SETFL, O_NONBLOCK);




…




// start before calling select

FD_ZERO (&amp;readfds);

FD_SET (sock, &amp;readfds);




// set the timer

tv.tv_sec = 1;

tv.tv_usec = 0;




// call select

rv = select (sock + 1, &amp;readfds, NULL, NULL, &amp;tv);         // sock is the socket you are using




if (rv == 0)

{

// timeout, no data

}

else if (rv == 1)

{

// there is data to be received

}







<strong>Important note</strong>

The server closes the file and terminates execution after the message with zero bytes arrives. If the ack sent for that last message does not make it to the client, the client will keep resending it forever to a non-responding server. To avoid that, the client will start a counter after sending a message with zero bytes and will only resend that last message 3 times. After 3 times, it will return to the main function.







<strong>Requirements to complete the lab</strong>

Show the TA correct execution of the programs you wrote and upload source code to Camino.




Be sure to retain copies (machine and/or printed) of your source code. You will want these for study purposes and to resolve any grading questions (should they arise)




Please start each program with a descriptive block that includes minimally the following information:

/*

* Name: &lt;your name&gt;

* Date:

* Title: Lab6 – ….

* Description: This program … &lt;you should

* complete an appropriate description here.&gt;

*/




<strong> </strong>

<strong>Rdt3.0</strong><strong> sender – FSM</strong>

<strong> </strong>