# Dokploy hosts

## Deployment

Install Tailscale on them

```sh
curl -fsSL https://tailscale.com/install.sh | sh && sudo tailscale up --auth-key=tskey-auth-<key> --advertise-exit-node
```

Enable Tailscale SSH

```sh
sudo tailscale set --ssh
```

Add these [public]ssh keys

- `fs_home_rsa` is for admin access
- `dokploy` is for Dokploy

```sh
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCpjSKK4qiMx4vIOvX7PHBOOctpYQ/XQQKWinw+v8oIQoI3GWkdRTwZpXJ2QSor/10zk5TZphP6XpfXxJj3caPwZPnu/ZFci/Iy40T6O2PDUFBjzaBLoIRci4lkRgjyEITKt9K1gIiqO8CnrMNBQTYj8gt7pHa3jIv102M1JIVqq4IU6tDTnf6Nku20jQcvxQCuJT0AszLZwMsD8IMOPkOfztnYOeJTXKOvcT+Vff3+ORXtXbVXNvAhobiSdK1MH5dAMsDZs9QcAazJGMfp50BcBUiHCRUo2XRk+IjMt7Tj6EjI+IMy+QOQWvTM016X9xTiLrPEJMU2RatfeG9VvcCPeQxPCbQE7uuYvCa3SAeJ3CTSL6kTE/4gp4uIq/XZEgZZO/4vuWF+1cNRYhePyJm9tlIU1o5AHHL2I8FJUlQJAe/+gRd/irfzRGDhiYw3fa02nFXsPY4mlEjIdjAd7JYRv1D3X2LBS+62PjqRC3NoNLodfywd3pVsiO3l3QsQKMRGxbyA9jSelSORNftGNeIQJWgJXW0ws42aCYmdcarCpLIil5QfV3WSfXz+a+wd5y7OCW19+sl3j1RHJhIuttsAZQOIGisCfDgstxhY08yuqA2DcZCdNL50JJzN2AQyeVzGRNEhFFEELBdRMAOf7L61Qie3Y+s9aN0do0xDInOkYQ== fs@home
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILgy+u5ml2a+xDaxFCPhIXLJh162jF2hI0PRkiVxudwF
```