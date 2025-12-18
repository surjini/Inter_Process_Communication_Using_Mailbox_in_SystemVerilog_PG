## Inter_Process_Communication_Using_Mailbox_in_SystemVerilog_PG
EXPERIMENT 8– MAILBOX–BASED PACKET TRANSFER USING SYSTEMVERILOG

## Aim
To design and verify a SystemVerilog testbench that uses mailbox to transfer randomized packet objects from a Generator to a Driver, demonstrating synchronised communication in verification environments.

 ## Tool Used
Xilinx Vivado Simulator

## Theory

In SystemVerilog, mailbox is a built-in communication mechanism that allows safe transfer of data between parallel processes such as driver and generator.
It supports synchronous and asynchronous communication and acts like a queue storing objects.

## Features of Mailbox

Used to share data between threads
Supports put(), get(), try_put(), try_get()
Can be bounded (size-limited) or unbounded

## SYSTEM VERILOG
```
mail box
//-------------------------------------------------------------------------
// Packet 
//-------------------------------------------------------------------------
class packet;
  rand bit [7:0] addr;
  rand bit [7:0] data;

  //Displaying randomized values
  function void post_randomize();
    $display("Packet::Packet Generated");
    $display("Packet::Addr=%0d,Data=%0d",addr,data);
  endfunction

endclass
//Generator - Generates the transaction packet and send to driver
//-------------------------------------------------------------------------
class generator;
  packet pkt;
  mailbox m_box;
  //constructor, getting mailbox handle
  function new(mailbox m_box);
    this.m_box = m_box;
  endfunction
  task run;
    repeat(2) begin
      pkt = new();
      pkt.randomize(); //generating packet
      m_box.put(pkt);  //putting packet into mailbox
      $display("Generator::Packet Put into Mailbox");
      #5;
    end
  endtask
endclass

// Driver - Gets the packet from generator and display's the packet items
//-------------------------------------------------------------------------
class driver;
  packet pkt;
  mailbox m_box;

  //constructor, getting mailbox handle
  function new(mailbox m_box);
    this.m_box = m_box;
  endfunction
  task run;
    repeat(2) begin
      m_box.get(pkt); //getting packet from mailbox
      $display("Driver::Packet Recived");
      $display("Driver::Addr=%0d,Data=%0d\n",pkt.addr,pkt.data);
    end
  endtask
endclass

module mailbox_ex;
  generator gen;
  driver    dri;
  mailbox m_box; //declaring mailbox m_box

  initial begin
    //Creating the mailbox, Passing the same handle to generator and driver, 
    //because same mailbox should be shared in-order to communicate.
    m_box = new(); //creating mailbox

    gen = new(m_box); //creating generator and passing mailbox handle
    dri = new(m_box); //creating driver and passing mailbox handle
    $display("------------------------------------------");
    fork
      gen.run(); //Process-1
      dri.run(); //Process-2
    join
    $display("------------------------------------------");
  end
endmodule
```

## OUTPUT

## RESULT
The packet objects were successfully randomized.
Packets were passed from generator → mailbox → driver.
The driver received and printed all packet information.
Thus, mailbox-based synchronization between verification components is implemented correctly.
Hence, inter-process data transfer using mailbox in SystemVerilog is verified successfully.

