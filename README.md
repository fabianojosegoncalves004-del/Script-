variavel podeVoar = falso
variavel velocidadeVoo = 10
variavel cooldownAtaque = 2 segundos
variavel ultimoAtaque = 0

funcao ativarVoo():
    podeVoar = verdadeiro

funcao desativarVoo():
    podeVoar = falso

funcao moverVoo(direcao):
    se podeVoar:
        mover personagem na direcao com velocidadeVoo

funcao atacar():
    se tempoAtual - ultimoAtaque >= cooldownAtaque:
        criar bolaDeFogo na posicao do personagem
        definir direcao da bolaDeFogo para frente
        lançar bolaDeFogo
        ultimoAtaque = tempoAtual

funcao atualizar():
    se teclaVooPressionada:
        ativarVoo()
    senao:
        desativarVoo()

    se podeVoar:
        moverVoo(entradaDoJogador)

    se teclaAtaquePressionada:
        atacar()using UnityEngine;

public class PlayerFlightAndFire : MonoBehaviour
{
    public float flightSpeed = 10f;
    public GameObject fireballPrefab;
    public Transform firePoint;
    public float fireCooldown = 2f;

    private bool canFly = false;
    private float lastFireTime = 0f;

    void Update()
    {
        // Ativar/desativar voo com tecla F
        if (Input.GetKeyDown(KeyCode.F))
        {
            canFly = !canFly;
        }

        if (canFly)
        {
            Fly();
        }

        // Atacar com bola de fogo com tecla mouse esquerdo
        if (Input.GetMouseButtonDown(0) && Time.time > lastFireTime + fireCooldown)
        {
            Fire();
        }
    }

    void Fly()
    {
        float moveHorizontal = Input.GetAxis("Horizontal");
        float moveVertical = Input.GetAxis("Vertical");
        float moveUpDown = 0;

        if (Input.GetKey(KeyCode.E)) // Sobe
            moveUpDown = 1;
        else if (Input.GetKey(KeyCode.Q)) // Desce
            moveUpDown = -1;

        Vector3 movement = new Vector3(moveHorizontal, moveUpDown, moveVertical);
        transform.Translate(movement * flightSpeed * Time.deltaTime, Space.World);
    }

    void Fire()
    {
        Instantiate(fireballPrefab, firePoint.position, firePoint.rotation);
        lastFireTime = Time.time;
    }
}using UnityEngine;

public class Fireball : MonoBehaviour
{
    public float speed = 20f;
    public float damage = 25f;
    public float lifetime = 5f;

    void Start()
    {
        Destroy(gameObject, lifetime);
    }

    void Update()
    {
        transform.Translate(Vector3.forward * speed * Time.deltaTime);
    }

    void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Enemy"))
        {
            // Supondo que o inimigo tenha um script com método TakeDamage
            other.GetComponent<Enemy>().TakeDamage(damage);
            Destroy(gameObject);
        }
        else if (!other.CompareTag("Player"))
        {
            Destroy(gameObject);
        }
    }
