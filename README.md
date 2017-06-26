# bin

The binary manipulation library make file management a simple task.</br>
Allows for a wide range of features that can be used, the library is a bit of a mess though and isn't at a point that I am happy with yet.</br>
Basic usage:
```lua
require("bin")
print("TEST - 1")
test=bin.new("I am a string in a bin")
print(test)
print("TEST - 2: Writing a test file to disk")
test2=bin.freshStream("newFileToStreamWriteTo.dat",false)
test2:addBlock(1234,4)
test2:addBlock("Hello",5)
test2:addBlock(true) -- always 1 and a lua type
test2:addBlock({1,2,3,4,5}) -- depends and is a lua type
test2:close()
print("test 2 done")
print("TEST - 3: reading the file we wrote to disk")
test3=bin.load("newFileToStreamWriteTo.dat") -- binary file
nL,nB=test3:getBlock("n",4) -- reads the first 4 bytes as a number
-- litte endian, big endian
print(nL,nB)
str=test3:getBlock("s",5)
print(str)
bool=test3:getBlock("b")
print(bool)
tab=test3:getBlock("t")
print(tab)
```
# Output
```
TEST - 1
I am a string in a bin
TEST - 2: Writing a test file to disk
test 2 done
TEST - 3: reading the file we wrote to disk
1234	3523477504
Hello
true
table: 0x001e3f40
```

# The bin library has all of these features

Note: Examples of everything in action wll be made eventually...</br>
nil					 = log(data,name,fmt)  -- data is the text that you want to log to a file, the name argument only needs to be called with the first log. It tells where to log to. If name is used again it will change the location of the log file.
string,string,string = bin.getLuaVersion() -- returns PUC/JIT,major,minor

Constructors
------------
binobj		=	bin.load(filename,s,r)						-- creates binobj from file in s and r nil then reads entire file but if not s is the start point of reading and r is either the #to read after s or from s to '#' (like string.sub())
binobj		=	bin.new(string data)						-- creates binobj from a string
binobj		=	bin.stream(file,lock)						-- creates a streamable binobj lock is defult to true if locked file is read only
binobj		=	bin.newTempFile(data)						-- creates a tempfile in stream mode
bitobj		=	bits.new(n)									-- creates bitobj from a number
vfs			=	bin.newVFS()								-- creates a new virtual file system 	--Beta
vfs			=	bin.loadVFS(path)							-- loads a saved .lvfs file				--Beta
buf			=	bin:newDataBuffer(s)						-- creates a databuffer
binobj		=	bin.bufferToBin(b)							-- converts a buffer object to a bin object
buf			=	bin.binToBuffer(b)							-- converts a bin object to a buffer obj
buf			=	bin:getDataBuffer(a,b)						-- gets a speical buffer that opperates on a streamed file. It works just like a regular data buffer
blockWriter =	bin.newNamedBlock(indexSize)				-- returns a block writer object index size is the size of the index where labels and pointers are stored
blockWriter =	bin.newStreamedNamedBlock(indexSize,path)	-- returns a streamed version of the above path is the path to write the file
blockReader =	bin.loadNamedBlock(path)					-- returns a block reader object, path is where the file is located
blockHandler=	bin.namedBlockManager(arg)					-- returns a block handler object, if arg is a string it will loade a named block file, if its a number or nil it will create a nambed block object

Note: the blockWriter that isn't streamed needs to have tofile(path) called on it to write it to a file
Note: the streamed blockWriter must have the close method used when you are done writing to it!

Helpers
-------
string	=	bin.randomName(n,ext)					-- creates a random file name if n and ext is nil then a random length is used, and '.tmp' extension is added
string	=	bin.NumtoHEX(n)							-- turns number into hex
binobj	=	bin.HEXtoBin(s)*D						-- turns hex data into binobj
string	=	bin.HEXtoStr(s)*D						-- turns hex data into string/text
string	=	bin.tohex(s)							-- turns string to hex
string	=	bin.fromhex(s)							-- turns hex to string
string	=	bin.endianflop(data)					-- flips the high order bits to the low order bits and viseversa
string	=	bin.getVersion()						-- returns the version as a string
string	=	bin.escapeStr(str)						-- system function that turns functions into easy light
string	=	bin.ToStr(tab)							-- turns a table into a string (even functions are dumped; used to create compact data files)
nil		=	bin.packLLIB(name,tab,ext)				-- turns a bunch of 'files' into 1 file tab is a table of file names, ext is extension if nil .llib is used Note: Currently does not support directories within .llib
nil		=	bin.unpackLLIB(name,exe,todir,over,ext)	-- takes that file and makes the files Note: if exe is true and a .lua file is in the .llib archive than it is ran after extraction ext is extension if nil .llib is used
boolean	=	bin.fileExist(path)						-- returns true if the file exist false otherwise
boolean\*=	bin.closeto(a,b,v)						-- test data to see how close it is (a,b=tested data v=#difference (v must be <=255))
String	=	bin.textToBinary(txt)					-- turns text into binary data 10101010's
binobj	=	bin.decodeBits(bindata)					-- turns binary data into text
string	=	bin.trimNul(s)							-- terminates string at the nul char
number	=	bin.getIndexSize(tab)					-- used to get the index size of labels given to a named block
string	=	bits.numToBytes(num,occ)				-- returns the number in base256 string data, occ is the space the number will take up

Assessors
---------
nil\*\*\*	=	binobj:tofile(filename)					-- writes binobj data as a file
binobj\*	=	binobj:clone()							-- clones and returns a binobj
number\*	=	binobj:compare(other binobj,diff)		-- returns 0-100 % of simularity based on diff factor (diff must be <=255)
string	=	binobj:sub(a,b)							-- returns string data like segment but dosen't alter the binobject
num,num	=	binobj:tonumber(a,b)					-- converts from a-b (if a and b are nil it uses the entire binobj) into a base 10 number so 'AXG' in data becomes 4675649 returns big,little endian
number	=	binobj:getbyte(n)						-- gets byte at location and converts to base 10 number
bitobj	=	binobj:tobits(i)						-- returns the 8bits of data as a bitobj Ex: if value of byte was a 5 it returns a bitobj with a value of: '00000101'
string	=	binobj:getHEX(a,b)						-- gets the HEX data from 'a' to 'b' if both a,b are nil returns entire file as hex
a,b		=	binobj:scan(s,n,f)						-- searches a binobj for 's'; n is where to start looking, 'f' is weather or not to flip the string data entered 's'
string	=	binobj:streamData(a,b)					-- reads data from a to b or a can be a data handle... I will explain this and more in offical documentation
string#	=	binobj:streamread(a,b)					-- reads data from a stream object between a and b (note: while other functions start at 1 for both stream and non stream 0 is the starting point for this one)
boolean	=	binobj:canStreamWrite()					-- returns true if the binobj is streamable and isn't locked
string	=	bitobj:conv(n)							-- converts number to binary bits (system used)
binobj	=	bitobj:tobytes()						-- converts bit obj into a string byte (0-255)
number	=	bitobj:tonumber()						-- converts '10101010' to a number
boolean	=	bitobj:isover()							-- returns true if the bits exceed 8 bits false if 8 or less
string	=	bitobj:getBin()							-- returns the binary 10100100's of the data as a string
string	=	binobj:getHash(n)						-- returns a Hash of a file (This is my own method of hashing btw) n is the length you want the hash to be
string	=	binobj:getData()						-- returns the bin object as a string
depends =	blockReader:getBlock(name)				-- returns the value associated with the name, values can be any lua data except userdata

Mutators (Changes affect the actual object or if streaming the actual file) bin:remove()
--------
nil		=	binobj:setEndOfFile(n)	-- sets the end of a file
nil		=	binobj:reverse() 		-- reverses binobj data ex: hello --> olleh
nil		=	binobj:flipbits() 		-- flips the binary bits
nil\*\* 	=	binobj:segment(a,b)		-- gets a segment of the binobj data works just like string.sub(a,b) without str
nil\*	=	binobj:insert(a,i)		-- inserts i (string or number(converts into string)) in position a
nil\*	=	binobj:parseN(n)		-- removes ever (nth) byte of data
nil 	=	binobj:getlength()		-- gets length or size of binary data
nil\*	=	binobj:shift(n)			-- shift the binary data by n positive --> negitive <--
nil\*	=	binobj:delete(a,b)		-- deletes part of a binobj data Usage: binobj:delete(1) deletes at pos 1 binobj:delete(1,10) deletes from 1 to 10, binobj:delete('string') removes all instances of 'string' from the object
nil\*	=	binobj:encrypt(seed)	-- encrypts data using a seed, seed may be left blank
nil\*	=	binobj:decrypt(seed)	-- decrypts data encrypted with encrypt(seed)
nil\*	=	binobj:shuffle()		-- Shuffles the data randomly Note: there is no way to get it back!!! If original is needed clone beforehand
nil\*\*	=	binobj:mutate(a,i)		-- changes position a's value to i
nil		=	binobj:merge(o,t)		-- o is the binobj you are merging if t is true it merges the new data to the left of the binobj EX: b:merge(o,true) b='yo' o='data' output: b='datayo' b:merge(o) b='yo' o='data' output: b='yodata'
nil\*	=	binobj:parseA(n,a,t)	-- n is every byte where you add, a is the data you are adding, t is true or false true before false after
nil		=	binobj:getHEX(a,b)		-- returns the HEX of the bytes between a,b inclusive
nil		=	binobj:cryptM()			-- a mirrorable encryptor/decryptor
nil		=	binobj:addBlock(d,n)	-- adds a block of data to a binobj s is size d is data e is a bool if true then encrypts string values. if data is larger than 'n' then data is lost. n is the size of bytes the data is Note: n is no longer needed but you must use getBlock(type) to get it back
nil		=	binobj:getBlock(t,n)	-- gets block of code by type
nil		=	binobj:seek(n)			-- used with getBlock EX below with all 3
nil\*	=	binobj:morph(a,b,d)		-- changes data between point a and b, inclusive, to d
nil		=	binobj:fill(n,d)		-- fills binobj with data 'd' for n
nil		=	binobj:fillrandom(n)	-- fills binobj with random data for n
nil		=	binobj:shiftbits(n)		-- shifts all bits by n amount
nil		=	binobj:shiftbit(n,i)	-- shifts a bit ai index i by n
nil#	=	binobj:streamwrite(d,n)	-- writes to the streamable binobj d data n position
nil#	=	binobj:open()			-- opens the streamable binobj
nil#	=	binobj:close()			-- closes the streamable binobj
nil		=	binobj:wipe()			-- erases all data in the file
nil\*	=	binobj:tackB(d)			-- adds data to the beginning of a file
nil		=	binobj:tackE(d)			-- adds data to the end of a file
nil		=	binobj:parse(n,f)		-- loops through each byte calling function 'f' with the args(i,binobj,data at i)
nil		=	binobj:flipbit(i)		-- flips the binary bit at position i
nil\*	=	binobj:gsub()			-- just like string:gsub(), but mutates self
nil		=	blockWriter:addNamedBlock(name,value) -- writes a named block to the file with name 'name' and the value 'value'

Note: numbers are written in Big-endian use bin.endianflop(d) to filp to Little-endian

Note: binobj:tonumber() returns big,little endian so if printing do: b,l=binobj:tonumber() print(l) print(b)

nil		=	bitobj:add(i)		-- adds i to the bitobj i can be a number (base 10) or a bitobj
nil		=	bitobj:sub(i)		-- subs i to the bitobj i can be a number (base 10) or a bitobj
nil		=	bitobj:multi(i)		-- multiplys i to the bitobj i can be a number (base 10) or a bitobj
nil		=	bitobj:div(i)		-- divides i to the bitobj i can be a number (base 10) or a bitobj
nil		=	bitobj:flipbits()	-- filps the bits 1 --> 0, 0 --> 1
string	=	bitobj:getBin()		-- returns 1's & 0's of the bitobj

\# stream objects only
\* not compatible with stream files
\*\* works but do not use with large files or it works to some degree
\*\*\* in stream objects all changes are made directly to the file, so there is no need to do tofile()


# Discord
For real-time assistance with my libraries! A place where you can ask questions and get help with any of my libraries</br>
https://discord.gg/U8UspuA</br>
