# 使用Go语言的 generate_cert.go 工具快速生成SSL证书

## Go 语言提供了一个能快速生成SSL证书工具

	$GOROOT/src/crypto/tls/generate_cert.go


	generate_cert.go

	func main() {
		flag.Parse()

		if len(*host) == 0 {
			log.Fatalf("Missing required --host parameter")
		}

		...

	}

	$ go run $GOROOT/src/crypto/tls/generate_cert.go --host 域名/IP

	如：

	$ go run /usr/local/go/src/crypto/tls/generate_cert.go --host localhost

	会在命令执行的目录下生成 key.pem 和 cert.pem
