# RawUtils

This is a package to interface with fixed structures in files and packets.

# Examples
```
using RawUtils;

namespace MyProtocol {

class MPPacket : RawPacket {

	// Parse packet...
	public MPPacket(byte[] Raw) : base(Raw) {}

	// Construct packet
	public MPPacket(uint Command, byte[] Data) : base(8 + Data.Length)
	{
		// Write Header
		WriteByte(0, (byte)0xAA);
		
		this.Command = Command;
		this.Length = Length;
		this.Data = Data;
		
		// Calc checksum
		Checksum = CalcChecksum();
	}
	
	public byte Checksum {
		get { return ReadByte(1); }
		set { WriteByte(1, value); }
	}
	
	public uint Length {
		get { return ReadUInt16(2); }
		set { WriteUInt16(2, value); }
	}
	
	public uint Command {
		get { return ReadUInt16(4); }
		set { WriteUInt16(4, value); }
	}
	
	public byte[] Data
	{
		get { return ReadArray(6, Length); }
		set
		{ 
			if(value == null) throw new ArgumentNullException();
			if(value.Length != Length) throw new ArgumentException();
			
			WriteArray(6, value);
		}
	}
	
	public bool IsChecksumCorrect => Checksum == CalcChecksum();
	
	private byte CalcChecksum()
	{
		var P = getPacket();
		return Convert.ToByte(P.Sum() & 0xFF);
	}
}
```