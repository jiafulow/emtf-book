# Misc

## Overview

The following are a bunch of unorganized information about EMTF++.


### About the Phase-2 L1 TDR version of EMTF++

The C++ implementation of EMTF++ used for the Phase-2 L1 TDR studies has been deprecated. It can still be found in the [cms-l1t-offline/cmssw/](https://github.com/cms-l1t-offline/cmssw/) repository:

- <https://github.com/cms-l1t-offline/cmssw/blob/phase2-l1t-integration-CMSSW_10_6_1_patch2/L1Trigger/L1TMuonEndCap/src/experimental/Phase2SectorProcessor.cc>


### EMTF++ chamber numbers

The EMTF++ chamber numbers are encoded using a LUT:

- Total: 115
    - 54 from CSC + 54 from RPC, GE1/1, GE2/1 + 7 from ME0
- Key: `(subsystem, station, cscid, subsector)`
- Mapped: `chamber_number`

::: {dropdown} Code snippet to create the LUT

``` python
def find_emtf_chamber_initializer():
  default_value = -99
  lut = np.full((5,5,10,4), default_value, dtype=np.int32)
  lut[1,1,1,1] = 0   # ME1/1 sub 1
  lut[1,1,2,1] = 1   # ME1/1 sub 1
  lut[1,1,3,1] = 2   # ME1/1 sub 1
  lut[1,1,4,1] = 3   # ME1/2 sub 1
  lut[1,1,5,1] = 4   # ME1/2 sub 1
  lut[1,1,6,1] = 5   # ME1/2 sub 1
  lut[1,1,7,1] = 6   # ME1/3 sub 1
  lut[1,1,8,1] = 7   # ME1/3 sub 1
  lut[1,1,9,1] = 8   # ME1/3 sub 1
  lut[1,1,1,2] = 9   # ME1/1 sub 2
  lut[1,1,2,2] = 10  # ME1/1 sub 2
  lut[1,1,3,2] = 11  # ME1/1 sub 2
  lut[1,1,4,2] = 12  # ME1/2 sub 2
  lut[1,1,5,2] = 13  # ME1/2 sub 2
  lut[1,1,6,2] = 14  # ME1/2 sub 2
  lut[1,1,7,2] = 15  # ME1/3 sub 2
  lut[1,1,8,2] = 16  # ME1/3 sub 2
  lut[1,1,9,2] = 17  # ME1/3 sub 2
  lut[1,2,1,0] = 18  # ME2/1
  lut[1,2,2,0] = 19  # ME2/1
  lut[1,2,3,0] = 20  # ME2/1
  lut[1,2,4,0] = 21  # ME2/2
  lut[1,2,5,0] = 22  # ME2/2
  lut[1,2,6,0] = 23  # ME2/2
  lut[1,2,7,0] = 24  # ME2/2
  lut[1,2,8,0] = 25  # ME2/2
  lut[1,2,9,0] = 26  # ME2/2
  lut[1,3,1,0] = 27  # ME3/1
  lut[1,3,2,0] = 28  # ME3/1
  lut[1,3,3,0] = 29  # ME3/1
  lut[1,3,4,0] = 30  # ME3/2
  lut[1,3,5,0] = 31  # ME3/2
  lut[1,3,6,0] = 32  # ME3/2
  lut[1,3,7,0] = 33  # ME3/2
  lut[1,3,8,0] = 34  # ME3/2
  lut[1,3,9,0] = 35  # ME3/2
  lut[1,4,1,0] = 36  # ME4/1
  lut[1,4,2,0] = 37  # ME4/1
  lut[1,4,3,0] = 38  # ME4/1
  lut[1,4,4,0] = 39  # ME4/2
  lut[1,4,5,0] = 40  # ME4/2
  lut[1,4,6,0] = 41  # ME4/2
  lut[1,4,7,0] = 42  # ME4/2
  lut[1,4,8,0] = 43  # ME4/2
  lut[1,4,9,0] = 44  # ME4/2
  lut[1,1,3,3] = 45  # ME1/1 neigh
  lut[1,1,6,3] = 46  # ME1/2 neigh
  lut[1,1,9,3] = 47  # ME1/3 neigh
  lut[1,2,3,3] = 48  # ME2/1 neigh
  lut[1,2,9,3] = 49  # ME2/2 neigh
  lut[1,3,3,3] = 50  # ME3/1 neigh
  lut[1,3,9,3] = 51  # ME3/2 neigh
  lut[1,4,3,3] = 52  # ME4/1 neigh
  lut[1,4,9,3] = 53  # ME4/2 neigh
  #
  lut[3,1,1,1] = 54  # GE1/1 sub 1
  lut[3,1,2,1] = 55  # GE1/1 sub 1
  lut[3,1,3,1] = 56  # GE1/1 sub 1
  lut[2,1,4,1] = 57  # RE1/2 sub 1
  lut[2,1,5,1] = 58  # RE1/2 sub 1
  lut[2,1,6,1] = 59  # RE1/2 sub 1
  lut[2,1,7,1] = 60  # RE1/3 sub 1
  lut[2,1,8,1] = 61  # RE1/3 sub 1
  lut[2,1,9,1] = 62  # RE1/3 sub 1
  lut[3,1,1,2] = 63  # GE1/1 sub 2
  lut[3,1,2,2] = 64  # GE1/1 sub 2
  lut[3,1,3,2] = 65  # GE1/1 sub 2
  lut[2,1,4,2] = 66  # RE1/2 sub 2
  lut[2,1,5,2] = 67  # RE1/2 sub 2
  lut[2,1,6,2] = 68  # RE1/2 sub 2
  lut[2,1,7,2] = 69  # RE1/3 sub 2
  lut[2,1,8,2] = 70  # RE1/3 sub 2
  lut[2,1,9,2] = 71  # RE1/3 sub 2
  lut[3,2,1,0] = 72  # GE2/1
  lut[3,2,2,0] = 73  # GE2/1
  lut[3,2,3,0] = 74  # GE2/1
  lut[2,2,4,0] = 75  # RE2/2
  lut[2,2,5,0] = 76  # RE2/2
  lut[2,2,6,0] = 77  # RE2/2
  lut[2,2,7,0] = 78  # RE2/2
  lut[2,2,8,0] = 79  # RE2/2
  lut[2,2,9,0] = 80  # RE2/2
  lut[2,3,1,0] = 81  # RE3/1
  lut[2,3,2,0] = 82  # RE3/1
  lut[2,3,3,0] = 83  # RE3/1
  lut[2,3,4,0] = 84  # RE3/2
  lut[2,3,5,0] = 85  # RE3/2
  lut[2,3,6,0] = 86  # RE3/2
  lut[2,3,7,0] = 87  # RE3/2
  lut[2,3,8,0] = 88  # RE3/2
  lut[2,3,9,0] = 89  # RE3/2
  lut[2,4,1,0] = 90  # RE4/1
  lut[2,4,2,0] = 91  # RE4/1
  lut[2,4,3,0] = 92  # RE4/1
  lut[2,4,4,0] = 93  # RE4/2
  lut[2,4,5,0] = 94  # RE4/2
  lut[2,4,6,0] = 95  # RE4/2
  lut[2,4,7,0] = 96  # RE4/2
  lut[2,4,8,0] = 97  # RE4/2
  lut[2,4,9,0] = 98  # RE4/2
  lut[3,1,3,3] = 99  # GE1/1 neigh
  lut[2,1,6,3] = 100 # RE1/2 neigh
  lut[2,1,9,3] = 101 # RE1/3 neigh
  lut[3,2,3,3] = 102 # GE2/1 neigh
  lut[2,2,9,3] = 103 # RE2/2 neigh
  lut[2,3,3,3] = 104 # RE3/1 neigh
  lut[2,3,9,3] = 105 # RE3/2 neigh
  lut[2,4,3,3] = 106 # RE4/1 neigh
  lut[2,4,9,3] = 107 # RE4/2 neigh
  #
  lut[4,1,1,1] = 108 # ME0 sub 1
  lut[4,1,2,1] = 109 # ME0 sub 1
  lut[4,1,3,1] = 110 # ME0 sub 1
  lut[4,1,1,2] = 111 # ME0 sub 2
  lut[4,1,2,2] = 112 # ME0 sub 2
  lut[4,1,3,2] = 113 # ME0 sub 2
  lut[4,1,1,3] = 114 # ME0 neigh
  lut[4,1,2,3] = 114 # ME0 neigh
  lut[4,1,3,3] = 114 # ME0 neigh
  return lut
```

:::

### EMTF++ 'site' numbers

The EMTF++ site numbers are encoded using a LUT:

- Total: 12
    - 5 from CSC + 4 from RPC + 3 from GEM
- Key: `(subsystem, station, ring)`
- Mapped: `site_number`

::: {dropdown} Code snippet to create the LUT

``` python
def find_emtf_site_initializer():
  default_value = -99
  lut = np.full((5,5,5), default_value, dtype=np.int32)
  lut[1,1,4] = 0  # ME1/1a
  lut[1,1,1] = 0  # ME1/1b
  lut[1,1,2] = 1  # ME1/2
  lut[1,1,3] = 1  # ME1/3
  lut[1,2,1] = 2  # ME2/1
  lut[1,2,2] = 2  # ME2/2
  lut[1,3,1] = 3  # ME3/1
  lut[1,3,2] = 3  # ME3/2
  lut[1,4,1] = 4  # ME4/1
  lut[1,4,2] = 4  # ME4/2
  lut[2,1,2] = 5  # RE1/2
  lut[2,1,3] = 5  # RE1/3
  lut[2,2,2] = 6  # RE2/2
  lut[2,2,3] = 6  # RE2/3
  lut[2,3,1] = 7  # RE3/1
  lut[2,3,2] = 7  # RE3/2
  lut[2,3,3] = 7  # RE3/3
  lut[2,4,1] = 8  # RE4/1
  lut[2,4,2] = 8  # RE4/2
  lut[2,4,3] = 8  # RE4/3
  lut[3,1,1] = 9  # GE1/1
  lut[3,2,1] = 10 # GE2/1
  lut[4,1,4] = 11 # ME0
  return lut
```

:::

### EMTF++ 'host' numbers

The EMTF++ host numbers are encoded using a LUT:

- Total: 19
    - 9 from CSC + 9 from RPC, GE1/1, GE2/1 + 1 from ME0
- Key: `(subsystem, station, ring)`
- Mapped: `host_number`

::: {dropdown} Code snippet to create the LUT

``` python
def find_emtf_host_initializer():
  default_value = -99
  lut = np.full((5,5,5), default_value, dtype=np.int32)
  lut[1,1,4] = 0  # ME1/1a
  lut[1,1,1] = 0  # ME1/1b
  lut[1,1,2] = 1  # ME1/2
  lut[1,1,3] = 2  # ME1/3
  lut[1,2,1] = 3  # ME2/1
  lut[1,2,2] = 4  # ME2/2
  lut[1,3,1] = 5  # ME3/1
  lut[1,3,2] = 6  # ME3/2
  lut[1,4,1] = 7  # ME4/1
  lut[1,4,2] = 8  # ME4/2
  lut[3,1,1] = 9  # GE1/1
  lut[2,1,2] = 10 # RE1/2
  lut[2,1,3] = 11 # RE1/3
  lut[3,2,1] = 12 # GE2/1
  lut[2,2,2] = 13 # RE2/2
  lut[2,2,3] = 13 # RE2/3
  lut[2,3,1] = 14 # RE3/1
  lut[2,3,2] = 15 # RE3/2
  lut[2,3,3] = 15 # RE3/3
  lut[2,4,1] = 16 # RE4/1
  lut[2,4,2] = 17 # RE4/2
  lut[2,4,3] = 17 # RE4/3
  lut[4,1,1] = 18 # ME0
  return lut
```

:::


### EMTF++ zone definition

EMTF++ divides the 1.2 &le; |&eta;| &le; 2.4 region into 3 (overlapping) zones. Each zone is defined using a pair of integer theta values which denote the lower and upper edges of the zone.

- Key: `(host_number, zone_number)`
- Mapped: `(lower_theta, upper_theta)`

::: {dropdown} Code snippet to create the LUT

``` python
def find_emtf_zones_initializer():
  default_value = -99
  lut = np.full((19,3,2), default_value, dtype=np.int32)
  lut[0,0] = 4,26    # ME1/1
  lut[3,0] = 4,25    # ME2/1
  lut[5,0] = 4,25    # ME3/1
  lut[7,0] = 4,25    # ME4/1
  lut[9,0] = 17,26   # GE1/1
  lut[12,0] = 7,25   # GE2/1
  lut[14,0] = 4,25   # RE3/1
  lut[16,0] = 4,25   # RE4/1
  lut[18,0] = 4,23   # ME0
  #
  lut[0,1] = 24,53   # ME1/1
  lut[1,1] = 46,54   # ME1/2
  lut[3,1] = 23,49   # ME2/1
  lut[5,1] = 23,41   # ME3/1
  lut[6,1] = 44,54   # ME3/2
  lut[7,1] = 23,35   # ME4/1
  lut[8,1] = 38,54   # ME4/2
  lut[9,1] = 24,52   # GE1/1
  lut[10,1] = 52,56  # RE1/2
  lut[12,1] = 23,46  # GE2/1
  lut[14,1] = 23,36  # RE3/1
  lut[15,1] = 40,52  # RE3/2
  lut[16,1] = 23,31  # RE4/1
  lut[17,1] = 35,54  # RE4/2
  #
  lut[1,2] = 52,88   # ME1/2
  lut[4,2] = 52,88   # ME2/2
  lut[6,2] = 50,88   # ME3/2
  lut[8,2] = 50,88   # ME4/2
  lut[10,2] = 52,84  # RE1/2
  lut[13,2] = 52,88  # RE2/2
  lut[15,2] = 48,84  # RE3/2
  lut[17,2] = 52,84  # RE4/2
  return lut
```

:::

### EMTF++ timezone definition

EMTF++ divides the BX values into 3 (overlapping) timezones. Each timezone is defined using a pair of BX values which denote the first and last BX values of the timezone.

- Key: `(host_number, timezone_number)`
- Mapped: `(first_bx, last_bx)`

::: {dropdown} Code snippet to create the LUT

``` python
def find_emtf_timezones_initializer():
  default_value = -99
  lut = np.full((19,3,2), default_value, dtype=np.int32)
  lut[0,0] = -1,0   # ME1/1
  lut[1,0] = -1,0   # ME1/2
  lut[2,0] = -1,0   # ME1/3
  lut[3,0] = -1,0   # ME2/1
  lut[4,0] = -1,0   # ME2/2
  lut[5,0] = -1,0   # ME3/1
  lut[6,0] = -1,0   # ME3/2
  lut[7,0] = -1,0   # ME4/1
  lut[8,0] = -1,0   # ME4/2
  lut[9,0] = -1,0   # GE1/1
  lut[10,0] = 0,0   # RE1/2
  lut[11,0] = 0,0   # RE1/3
  lut[12,0] = -1,0  # GE2/1
  lut[13,0] = 0,0   # RE2/2
  lut[14,0] = 0,0   # RE3/1
  lut[15,0] = 0,0   # RE3/2
  lut[16,0] = 0,0   # RE4/1
  lut[17,0] = 0,0   # RE4/2
  lut[18,0] = 0,0   # ME0
  #
  lut[:, 1] = lut[:, 0] - 1  # timezone 1 = timezone 0 - 1
  lut[:, 2] = lut[:, 1] - 1  # timezone 2 = timezone 1 - 1
  return lut
```

:::

