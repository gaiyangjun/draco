
## Sequential Connectivity Decoder

### ParseSequentialConnectivityData()

~~~~~
void ParseSequentialConnectivityData() {
  num_faces                                                                           I32
  num_points                                                                          I32
  connectivity_method                                                                 UI8
}
~~~~~
{:.draco-syntax }


### ParseSequentialIndicesUI8()

~~~~~
void ParseSequentialIndicesUI8() {
  for (i = 0; i < num_faces; ++i) {
    for (j = 0; j < 3; ++j) {
      face_to_vertex[j][i]                                                            UI8
    }
  }
}
~~~~~
{:.draco-syntax }


### ParseSequentialIndicesUI16()

~~~~~
void ParseSequentialIndicesUI16() {
  for (i = 0; i < num_faces; ++i) {
    for (j = 0; j < 3; ++j) {
      face_to_vertex[j][i]                                                            UI16
    }
  }
}
~~~~~
{:.draco-syntax }


### ParseSequentialIndicesUI32()

~~~~~
void ParseSequentialIndicesUI32() {
  for (i = 0; i < num_faces; ++i) {
    for (j = 0; j < 3; ++j) {
      face_to_vertex[j][i]                                                            UI32
    }
  }
}
~~~~~
{:.draco-syntax }


### DecodeSequentialIndices()

~~~~~
void DecodeSequentialIndices() {
  if (num_points < 256) {
    ParseSequentialIndicesUI8();
  } else if (num_points < (1 << 16)) {
    ParseSequentialIndicesUI16();
  } else {
    ParseSequentialIndicesUI32();
  }
}
~~~~~
{:.draco-syntax }


### DecodeSequentialCompressedIndices()

~~~~~
void DecodeSequentialCompressedIndices() {
  DecodeSymbols(num_faces * 3, 1, &decoded_symbols);
  last_index_value = 0;
  for (i = 0; i < num_faces; ++i) {
    for (j = 0; j < 3; ++j) {
      encoded_val = decoded_symbols[i * 3 + j];
      index_diff = (encoded_val >> 1);
      if (encoded_val & 1)
        index_diff = -index_diff;
      val = index_diff + last_index_value;
      face_to_vertex[j][i] = val;
      last_index_value = val;
    }
  }
}
~~~~~
{:.draco-syntax }


### DecodeSequentialConnectivityData()

~~~~~
void DecodeSequentialConnectivityData() {
  ParseSequentialConnectivityData();
  if (connectivity_method == 0) {
    DecodeSequentialCompressedIndices();
  } else if (connectivity_method == 1) {
    DecodeSequentialIndices();
  }
}
~~~~~
{:.draco-syntax }
