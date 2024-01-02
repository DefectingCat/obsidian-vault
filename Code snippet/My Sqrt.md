```go
func Sqrt(x float64) float64 {
	z := 1.0
	for {
		oldZ := z
		z -= (z*z - x) / (2 * z)
		if math.Abs(oldZ-z) <= 0.00000001 {
			break
		}
	}
	return z
}
```

[https://tour.go-zh.org/flowcontrol/8](https://tour.go-zh.org/flowcontrol/8)