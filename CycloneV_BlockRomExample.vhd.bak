---------------------------------------------------------
--	"Cyclone V Block ROM Example"
--
--	Description:
--		Simple interface to the Block ROM memory inside
--		Cyclone V device.
---------------------------------------------------------


library ieee;
use ieee.std_logic_1164.all;
use ieee.numeric_std.all;


entity CycloneV_BlockRomExample is
	
	port(
			-- User Input
			KEY				: in	std_logic_vector(3 downto 0);
			
			-- HEX/7-Segment Display
			HEX0			: out	std_logic_vector(6 downto 0);
			HEX1			: out	std_logic_vector(6 downto 0);
			
			-- LEDs
			LEDR			: out	std_logic_vector(9 downto 0);
			
			-- Clock
			CLOCK_50_B5B	: in	std_logic;
			
			-- Reset Button
			CPU_RESET_n		: in	std_logic
		);

end entity;


architecture rtl of CycloneV_BlockRomExample is
	
	
	-- Declare Block ROM Module
	component rom is
		port(
				q			: out	std_logic_vector(7 downto 0);	-- Block ROM Data Bus Out
				address		: in	std_logic_vector(7 downto 0);	-- Block ROM Address Bus In
				clock		: in	std_logic						-- Block ROM Clock In
				
			);
	end component;
	
	-- Clock Signals
	signal clk				: std_logic;					-- 5Hz clock for registering user inputs/ROM address.
	signal clk_count		: integer range 0 to 5000000;	-- Clock divider counter.
	
	-- Address Signals
	signal address			: integer range 0 to 255;		-- Address counter.
	
	-- Block ROM Signals
	signal rom_data			: std_logic_vector(7 downto 0);	-- Data from Block ROM.
	signal rom_address		: std_logic_vector(7 downto 0);	-- Address to Block ROM.
	signal rom_clk			: std_logic;					-- BLock ROM address register clock.
	
	-- HEX/Display Signals
	type hex_array_type
		is array (0 to 15) of std_logic_vector(6 downto 0);	-- Create an array. (16 words of 7 bits)
	signal hex_digit		: hex_array_type;				-- Create buffer to hold array.
	
	
begin
	
	
	-- Instantiate Block ROM module.
	BlockROM: rom
		port map(	q			=> rom_data,	-- Block ROM Data Bus Out
					address		=> rom_address,	-- Block ROM Address Bus In
					clock		=> rom_clk		-- Block ROM Clock In
				);
	
	
	-- CLOCK DIVIDER
	-- Slow the clock down to 5Hz. This clock is for registering user inputs so we want it slow
	-- enough not to register the switch bounces.
	process(CLOCK_50_B5B)
	begin
		if (rising_edge(CLOCK_50_B5B)) then
			if (clk_count = 5000000) then
				clk_count <= 0;
				clk <= not clk;
			else
				clk_count <= clk_count + 1;
			end if;
		end if;
	end process;
	
	
	-- UP / DOWN COUNTER
	-- Increment / Decrement the address counter.
	process(clk)
	begin
		if (rising_edge(clk)) then		-- On a clock pulse...
			if ( KEY(0) = '0' ) then	-- If Key 0 is pressed,
				address <= address + 1;	-- increment the address.
			elsif ( KEY(3) = '0' ) then	-- Is Key 3 is pressed,
				address <= address - 1;	-- decrement the address.
			end if;
		end if;
	end process;
	
	
	-- CONNECT SIGNALS TO ROM
	-- Connect adress bus to Block ROM and register address on clock pulse.
	rom_address <= std_logic_vector(to_unsigned(address,8));	-- Convert address from integer to standard logic vector.
	rom_clk <= clk;	-- Used to register address in ROM.
	
	
	-- DISPLAY ADDRESS
	-- Show address on red LEDs.
	LEDR(7 downto 0) <= rom_address;
	
	
	-- HEX DISPLAY
	-- Show the data retrieved from the Block ROM.
	hex_digit(0)  <= "0111111";	-- "0" Digit Bitmap
    hex_digit(1)  <= "0000110";	-- "1" Digit Bitmap
    hex_digit(2)  <= "1011011";	-- "2" Digit Bitmap
    hex_digit(3)  <= "1001111";	-- "3" Digit Bitmap
    hex_digit(4)  <= "1100110";	-- "4" Digit Bitmap
    hex_digit(5)  <= "1101101";	-- "5" Digit Bitmap
    hex_digit(6)  <= "1111101";	-- "6" Digit Bitmap
    hex_digit(7)  <= "0000111";	-- "7" Digit Bitmap
    hex_digit(8)  <= "1111111";	-- "8" Digit Bitmap
    hex_digit(9)  <= "1101111";	-- "9" Digit Bitmap
    hex_digit(10) <= "1110111";	-- "A" Digit Bitmap
    hex_digit(11) <= "1111100";	-- "b" Digit Bitmap
    hex_digit(12) <= "0111001";	-- "C" Digit Bitmap
    hex_digit(13) <= "1011110";	-- "d" Digit Bitmap
    hex_digit(14) <= "1111001";	-- "E" Digit Bitmap
    hex_digit(15) <= "1110001";	-- "F" Digit Bitmap
	HEX0 <= not hex_digit( to_integer(unsigned(rom_data( 3 downto  0))) );	-- Display lower 4-bits of ROM data.
	HEX1 <= not hex_digit( to_integer(unsigned(rom_data( 7 downto  4))) );	-- Display upper 4-bits of ROM data.
	
	
end rtl;
