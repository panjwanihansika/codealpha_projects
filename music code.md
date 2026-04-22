CODE



pip install music21 tensorflow numpy



from music21 import note, stream, tempo



\# Create a stream (a sequence of musical events)

s = stream.Stream()



\# Add some notes

s.append(note.Note('C4', quarterLength=1))

s.append(note.Note('D4', quarterLength=1))

s.append(note.Note('E4', quarterLength=1))

s.append(note.Note('F4', quarterLength=1))

s.append(note.Note('G4', quarterLength=1))



\# Add a tempo indication

s.append(tempo.MetronomeMark(number=120))



\# Save the stream as a MIDI file

s.write('midi', fp='input.mid')



print("Created 'input.mid' with a simple sequence of notes.")



import numpy as np

from music21 import converter, instrument, note, chord, stream

from tensorflow.keras.models import Sequential

from tensorflow.keras.layers import LSTM, Dense, Dropout



\# Load MIDI file (use your own .mid file here)

midi = converter.parse("input.mid")



notes = \[]



\# Extract notes

for element in midi.flat:

&#x20;   if isinstance(element, note.Note):

&#x20;       notes.append(str(element.pitch))

&#x20;   elif isinstance(element, chord.Chord):

&#x20;       notes.append('.'.join(str(n) for n in element.normalOrder))



\# Create sequences

sequence\_length = 10

network\_input = \[]

network\_output = \[]



for i in range(len(notes) - sequence\_length):

&#x20;   seq\_in = notes\[i:i+sequence\_length]

&#x20;   seq\_out = notes\[i+sequence\_length]

&#x20;   network\_input.append(seq\_in)

&#x20;   network\_output.append(seq\_out)



\# Map notes to integers

pitchnames = sorted(set(notes))

note\_to\_int = dict((note, number) for number, note in enumerate(pitchnames))



network\_input = \[\[note\_to\_int\[n] for n in seq] for seq in network\_input]



\# Reshape for LSTM

network\_input = np.reshape(network\_input, (len(network\_input), sequence\_length, 1))

network\_input = network\_input / float(len(pitchnames))



\# Build model

model = Sequential()

model.add(LSTM(128, input\_shape=(network\_input.shape\[1], network\_input.shape\[2]), return\_sequences=True))

model.add(Dropout(0.2))

model.add(LSTM(128))

model.add(Dense(len(pitchnames), activation='softmax'))



model.compile(loss='categorical\_crossentropy', optimizer='adam')



print("Model ready (training skipped for simplicity)")

