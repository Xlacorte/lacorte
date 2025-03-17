mvn
├── src
    ├── main
        ├── java
            └── com
                └── exemplo
                    └── moveis
                        ├── controller
                        ├── model
                        ├── repository
                        └── MóveisApplication.java
        ├── resources
            ├── templates
            ├── static
            └── application.properties


package com.exemplo.moveis.model;

public class Movel {
    private Long id;
    private String nome;
    private String descricao;
    private Double preco;
    
    // Getters e setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getNome() {
        return nome;
    }

    public void setNome(String nome) {
        this.nome = nome;
    }

    public String getDescricao() {
        return descricao;
    }

    public void setDescricao(String descricao) {
        this.descricao = descricao;
    }

    public Double getPreco() {
        return preco;
    }

    public void setPreco(Double preco) {
        this.preco = preco;
    }
}

package com.exemplo.moveis.repository;

import com.exemplo.moveis.model.Movel;
import java.util.ArrayList;
import java.util.List;

public class MovelRepository {
    private static List<Movel> moveis = new ArrayList<>();
    
    static {
        // Adicionando alguns móveis para exemplo
        Movel movel1 = new Movel();
        movel1.setId(1L);
        movel1.setNome("Sofá");
        movel1.setDescricao("Sofá confortável de 3 lugares");
        movel1.setPreco(1200.00);
        moveis.add(movel1);
        
        Movel movel2 = new Movel();
        movel2.setId(2L);
        movel2.setNome("Mesa de Jantar");
        movel2.setDescricao("Mesa de jantar com 6 cadeiras");
        movel2.setPreco(900.00);
        moveis.add(movel2);
    }

    public List<Movel> findAll() {
        return moveis;
    }
    
    public Movel findById(Long id) {
        return moveis.stream()
                     .filter(movel -> movel.getId().equals(id))
                     .findFirst()
                     .orElse(null);
    }
}


package com.exemplo.moveis.controller;

import com.exemplo.moveis.model.Movel;
import com.exemplo.moveis.repository.MovelRepository;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class MovelController {

    private final MovelRepository movelRepository;

    public MovelController() {
        this.movelRepository = new MovelRepository();  // Dependência simples, pode ser injetado com Spring
    }

    @GetMapping("/")
    public String listarMoveis(Model model) {
        model.addAttribute("moveis", movelRepository.findAll());
        return "index";
    }

    @GetMapping("/movel/{id}")
    public String verMovel(Model model, Long id) {
        Movel movel = movelRepository.findById(id);
        if (movel != null) {
            model.addAttribute("movel", movel);
            return "movel";
        }
        return "erro";
    }
}


<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Loja de Móveis</title>
</head>
<body>
    <h1>Lista de Móveis</h1>
    <ul>
        <li th:each="movel : ${moveis}">
            <a th:href="@{/movel/{id}(id=${movel.id})}" th:text="${movel.nome}"></a> - 
            <span th:text="${movel.preco}"></span>
        </li>
    </ul>
</body>
</html>


<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Detalhes do Móvel</title>
</head>
<body>
    <h1 th:text="${movel.nome}"></h1>
    <p th:text="${movel.descricao}"></p>
    <p>Preço: <span th:text="${movel.preco}"></span></p>
</body>
</html>

server.port=8080
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
spring.thymeleaf.mode=HTML

package com.exemplo.moveis;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MóveisApplication {

    public static void main(String[] args) {
        SpringApplication.run(MóveisApplication.class, args);
    }
}


mvn spring-boot:run

