---
PIP: 5
Title: Change in SprintLength
Description: Proposes a change in the SprintLength
Author: Sandeep Sreenath (@ssandeep), Paul O’Leary, Arpit Temani
Discussion: https://forum.polygon.technology/t/pip-5-change-in-sprintlength/10874/4
Status: Final
Type: Core
Date: 2023-01-10
---

## Motivation

Block reorgs are possible on the Polygon POS chain as the consensus mechanism bor uses is probabilistic - meaning that finality is eventual and typically based on the number of confirmations layered on top of the block holding your transaction. Although there has been a reduction in the frequency of reorgs with the introduction of the BDN, it is still prevalent and a cause for concern among DApp developers.

We have observed that reorg length is a function of sprint length. If a primary producer is down for some time and comes back online, then it will only affect one sprint as the primary producer will change for the next sprint.

Based on the above, we propose a decrease in the sprint length from 64 to 16 blocks. This means that a block producer produces blocks continuously for much lower time as compared with the current 128 sec. This will help a great deal in reducing the frequency and depth of reorgs. This doesn’t affect the total time/no of blocks a validator is producing over a span and hence there would be no change in the rewards overall.

## Specification

### Calculating what’s the sprint length to use

```go
func (c *BorConfig) CalculateSprint(number uint64) uint64 {
	return c.calculateSprintSizeHelper(c.Sprint, number)
}

func (c *BorConfig) calculateSprintSizeHelper(field map[string]uint64, number uint64) uint64 {
	keys := make([]string, 0, len(field))
	for k := range field {
		keys = append(keys, k)
	}

	sort.Strings(keys)

	for i := 0; i < len(keys)-1; i++ {
		valUint, _ := strconv.ParseUint(keys[i], 10, 64)
		valUintNext, _ := strconv.ParseUint(keys[i+1], 10, 64)

		if number >= valUint && number < valUintNext {
			return field[keys[i]]
		}
	}

	return field[keys[len(keys)-1]]
}
```

### Calculating sprint end block

```go
isSprintEnd := IsSprintStart(number+1, c.config.Sprint)
```

### New sprint length, producer delay types

```go
// BorConfig is the consensus engine config for Matic Bor based sealing.
type BorConfig struct {
	Period                   map[string]uint64      `json:"period"`                   // Number of seconds between blocks to enforce
	ProducerDelay            map[string]uint64      `json:"producerDelay"`            // Number of seconds delay between two producer intervals
	Sprint                   map[string]uint64      `json:"sprint"`                   // Epoch length to proposer
	BackupMultiplier         map[string]uint64      `json:"backupMultiplier"`         // Backup multiplier to determine the wiggle time
	ValidatorContract        string                 `json:"validatorContract"`        // Validator set contract
	StateReceiverContract    string                 `json:"stateReceiverContract"`    // State receiver contract
	OverrideStateSyncRecords map[string]int         `json:"overrideStateSyncRecords"` // Override state records count
	BlockAlloc               map[string]interface{} `json:"blockAlloc"`
	BurntContract            map[string]string      `json:"burntContract"` // Governance contract where the token will be sent to and burnt in London fork
	JaipurBlock              *big.Int               `json:"jaipurBlock"`   // Jaipur switch block (nil = no fork, 0 = already on Jaipur)
	DelhiBlock               *big.Int               `json:"delhiBlock"`    // Delhi switch block (nil = no fork, 0 = already on Delhi)
}
```

## Test Cases

```go
func TestSprintLengths(t *testing.T) {
	t.Parallel()

	testBorConfig := params.TestChainConfig.Bor
	testBorConfig.Sprint = map[string]uint64{
		"0": 16,
		"8": 4,
	}

	assert.Equal(t, testBorConfig.CalculateSprint(0), uint64(16))
	assert.Equal(t, testBorConfig.CalculateSprint(8), uint64(4))
	assert.Equal(t, testBorConfig.CalculateSprint(9), uint64(4))
}
```

```go
func TestSprintLengthReorg(t *testing.T) {
	t.Parallel()

	reorgsLengthTests := getTestSprintLengthReorgCases()
	f, err := os.Create("sprintReorg.csv")

	defer func() {
		err = f.Close()
		if err != nil {
			panic(err)
		}
	}()

	if err != nil {
		_log.Fatalln("failed to open file", err)
	}

	w := csv.NewWriter(f)
	err = w.Write([]string{"Induced Reorg Length", "Start Block", "Sprint Size", "Disconnected Node Id", "Disconnected Node Id's Reorg Length", "Observer Node Id's Reorg Length"})
	w.Flush()

	if err != nil {
		panic(err)
	}

	var wg sync.WaitGroup

	for index, tt := range reorgsLengthTests {
		if index%4 == 0 {
			wg.Wait()
		}

		wg.Add(1)
		go SprintLengthReorgIndividualHelper(t, index, tt, w, &wg)
	}
}
```

## References

* bor/config.go at 4aa56c543acdea6e441990220efb87f0ff723d98 · maticnetwork/bor · GitHub
* bor/config.go at 4aa56c543acdea6e441990220efb87f0ff723d98 · maticnetwork/bor · GitHub

## Copyright
All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
