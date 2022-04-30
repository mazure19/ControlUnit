# ControlUnit

//Control Unit

module ControlUnit(reset, clock, inst, status, MEM_RW, RW, ALU_src, ALU_op, PC_src, imm_sel, WB, C0);

input reset, clock;

output reg C0;

output reg ALU_src;

output reg PC_src;

output reg MEM_RW;

output reg RW;

output reg WB;

output reg [3:0] ALU_op;

output reg [1:0] imm_sel;

input [31:0] inst;

input [3:0] status;

//Rtype

always @(*)

if (reset ==0)

case (inst[6:0])

7'b0110011: begin

C0 = 1'b0;

ALU_src = 1'b0;

RW = 1'b1;

MEM_RW = 1'b1;

WB = 1'b1;

PC_src = 1'b0;

imm_sel = 2'b00;

ALU_op = {inst[30], inst [14:12]};

end

//Itype 

7'b0010011: begin

C0 = 1'b0;

ALU_src = 1'b1;

RW = 1'b1;

MEM_RW = 1'b1;

WB = 1'b1;

PC_src = 1'b0;

imm_sel = 2'b01;

ALU_op = {inst[30], inst [14:12]};

end

//LW

7'b0000011: begin

C0 = 1'b0;

ALU_src = 1'b1;

RW = 1'b1;

MEM_RW = 1'b1;

WB = 1'b0;

PC_src = 1'b0;

imm_sel = 2'b01;

ALU_op = 4'b0000;

end

//SW

7'b0100011: begin

C0 = 1'b0;

ALU_src = 1'b1;

RW = 1'b1;

MEM_RW = 1'b1;

WB = 1'b1;

PC_src = 1'b0;

imm_sel = 2'b01;

ALU_op = 4'b0000;

end

//Btype

7'b1100011: begin

C0 = 1'b0;

ALU_src = 1'b1;

RW = 1'b1;

MEM_RW = 1'b1;

WB = 1'b1;

imm_sel = 2'b01;

case (inst [14:12])

3'b000: begin

PC_src = status[0]? 1'b1 :1'b0;

end

3'b000: begin

PC_src = status[1]? 1'b1 :1'b0;

end

endcase

begin

ALU_op = 4'b0000;

C0 = 1'b0;

end

end

endcase

endmodule 

//Testbench

module ControlUnit_tb();

reg [31:0]inst;

reg reset;

reg clock;

reg [3:0]status;


wire C0;

wire PC_src;

wire [3:0]ALU_op;

wire [1:0]imm_sel;

wire WB;

wire ALU_src;

wire REG_RW;

wire MEM_RW;

ControlUnit dut(reset, clock, inst, status, MEM_RW, REG_RW, ALU_src, ALU_op,
 PC_src, imm_sel, WB, C0);

 

always begin

#5 clock= ~clock;

end


initial begin

clock = 0;

reset = 1;

#10 reset = 0;

status = 4'b0000;

#10 inst = 32'b00000000010000000000000010010011;

#10 inst = 32'b00000000001000001000000110110011;

#10 inst = 32'b00000000000100000000000000100011;

#10 inst = 32'b01000000001000010000010001100011;

end

endmodule
