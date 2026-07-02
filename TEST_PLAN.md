# TEST_PLAN

## Public Requirement Tests

1. Source codec round trip:
   - Chinese and ASCII UTF-8 text converts to bits and back.
2. Scrambler round trip:
   - XOR/PN scrambling is reversible with the same seed.
3. Frame structure:
   - Frame contains preamble, length, CRC, and payload.
   - Header parsing recovers original length and checksum.
4. QPSK mapping:
   - Required Gray-coded constellation mapping is exact.
5. AWGN reproducibility:
   - Same seed and SNR produces the same received sequence.
6. Synchronization:
   - Random pre-frame offsets from 0 to 128 symbols are detected within one
     symbol at SNR >= 12 dB.
7. End-to-end CLI:
   - `python main.py --input Test.txt --output results/received.txt --snr 12 --seed 2026 --mod qpsk --channel awgn`
     exits normally.
   - `results/received.txt` matches `Test.txt`.
   - `results/metrics.json` contains the required fields.
   - At least two required plots exist.
8. Low-SNR behavior:
   - Program does not crash and still writes metrics and received text.

## Hidden-Style Tests

- Different UTF-8 input lengths, including empty and longer Chinese text.
- Different fixed seeds.
- Random synchronization offsets.
- Parameter validation for unsupported modulation/channel.
- No hard-coded dependence on public `Test.txt` content.

## Level 3 Tests

1. Rayleigh + ZF:
   - Run `--channel rayleigh` at moderate SNR.
   - Confirm program exits normally, estimates a channel gain, equalizes before
     demodulation, and records `equalizer=ZF` in `metrics.json`.
2. Convolutional Viterbi:
   - Encode and decode random bits with `src.convolutional`.
   - Run the CLI with `--fec conv` and confirm output/metrics are generated.
3. Backward compatibility:
   - Re-run teacher `public_tests` to ensure optional modules do not break the
     required base AWGN/QPSK/repetition path.
