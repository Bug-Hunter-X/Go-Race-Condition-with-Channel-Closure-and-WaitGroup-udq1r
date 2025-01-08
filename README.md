# Go Race Condition with Channel Closure and WaitGroup

This repository demonstrates a subtle race condition in a Go program that uses channels and a `sync.WaitGroup`. The program appears to work correctly in many cases, but can panic under specific circumstances due to accessing a closed channel.

The core issue stems from the order of operations: goroutines are launched to read from the channel, the main goroutine sends data, then closes the channel. If a goroutine attempts to read from the channel *after* it's closed but *before* the `close(ch)` call completes, it will panic. The `defer wg.Done()` adds another layer of complexity since the panic might not always manifest, masking the problem.

The solution involves ensuring all goroutines have started reading from the channel before the channel is closed.

## Solution
The `bugSolution.go` file provides a corrected version of the code.  It uses a `sync.WaitGroup` to guarantee all goroutines are launched before any data is sent.