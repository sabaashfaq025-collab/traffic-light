# traffic-light
Designed and implemented a Moore Finite State Machine (FSM) traffic light controller in Verilog. The controller cycles through Red, Yellow, and Green states using synchronous state transitions, features an active-low asynchronous reset, and includes a testbench for functional simulation and FPGA implementation.
Verilog Code:
module traffic_light_controller (
    input wire clk,
    input wire rst_n,
    output reg [1:0] light_out // 2-bit output to represent 1, 2, or 3
);

    // State encoding
    localparam S_RED    = 2'b00,
               S_YELLOW = 2'b01,
               S_GREEN  = 2'b10;

    reg [1:0] state;

    // Single synchronous block for state transitions and outputs
    always @(posedge clk or negedge rst_n) begin
        if (!rst_n) begin
            state     <= S_RED;
            light_out <= 2'd1; // Starts at Red (Output = 1)
        end else begin
            case (state)
                S_RED: begin
                    light_out <= 2'd1;     // Output is 1
                    state     <= S_YELLOW; // Transitions Red -> Yellow
                end

                S_YELLOW: begin
                    light_out <= 2'd2;    // Output is 2
                    state     <= S_GREEN; // Transitions Yellow -> Green
                end

                S_GREEN: begin
                    light_out <= 2'd3;  // Output is 3
                    state     <= S_RED; // Transitions Green -> Red (loops back)
                end

                default: begin
                    state     <= S_RED;
                    light_out <= 2'd1;
                end
            endcase
        end
    end

endmodule
TestBench:
`timescale 1ns / 1ps

module tb_traffic_light_controller;

    // Inputs
    reg clk;
    reg rst_n;

    // Outputs
    wire [1:0] light_out;

    // Instantiate the Unit Under Test (UUT)
    traffic_light_controller uut (
        .clk(clk), 
        .rst_n(rst_n), 
        .light_out(light_out)
    );

    // Clock generation (50MHz)
    always #10 clk = ~clk;

    initial begin
        // Initialize Inputs
        clk = 0;
        rst_n = 0;

        // Hold reset for 20ns
        #20;
        rst_n = 1; // Release reset
        
        // Let the simulation run for a few clock cycles 
        // to watch the output cycle through 1 -> 2 -> 3 -> 1
        #120;
        
        $finish;
    end
      
endmodule

Vending Machine:
